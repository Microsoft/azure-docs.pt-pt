---
title: Copiar dados de/para o Azure SQL Data Warehouse
description: Saiba como copiar dados de/para o Azure SQL Data Warehouse usando Azure Data Factory
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
ms.openlocfilehash: d0306d891b327422383120ef322ece407829f7ed
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/06/2019
ms.locfileid: "73683050"
---
# <a name="copy-data-to-and-from-azure-sql-data-warehouse-using-azure-data-factory"></a>Copiar dados de e para o Azure SQL Data Warehouse usando Azure Data Factory
> [!div class="op_single_selector" title1="Selecione a versão do serviço de Data Factory que você está usando:"]
> * [Versão 1](data-factory-azure-sql-data-warehouse-connector.md)
> * [Versão 2 (versão atual)](../connector-azure-sql-data-warehouse.md)

> [!NOTE]
> Este artigo aplica-se à versão 1 do Data Factory. Se você estiver usando a versão atual do serviço de Data Factory, consulte [conector de SQL data warehouse do Azure na v2](../connector-azure-sql-data-warehouse.md).

Este artigo explica como usar a atividade de cópia no Azure Data Factory para mover dados de/para o Azure SQL Data Warehouse. Ele se baseia no artigo [atividades de movimentação de dados](data-factory-data-movement-activities.md) , que apresenta uma visão geral da movimentação de dados com a atividade de cópia.

> [!TIP]
> Para obter o melhor desempenho, use o polybase para carregar dados no Azure SQL Data Warehouse. A seção [usar o polybase para carregar dados no Azure SQL data warehouse](data-factory-azure-sql-data-warehouse-connector.md#use-polybase-to-load-data-into-azure-sql-data-warehouse) tem detalhes. Para obter instruções com um caso de uso, consulte [carregar 1 TB no Azure SQL data warehouse menos de 15 minutos com Azure data Factory](data-factory-load-sql-data-warehouse.md).

## <a name="supported-scenarios"></a>Cenários suportados
Você pode copiar dados **do Azure SQL data warehouse** para os seguintes armazenamentos de dados:

[!INCLUDE [data-factory-supported-sinks](../../../includes/data-factory-supported-sinks.md)]

Você pode copiar dados dos seguintes repositórios de dados **para o Azure SQL data warehouse**:

[!INCLUDE [data-factory-supported-sources](../../../includes/data-factory-supported-sources.md)]

> [!TIP]
> Ao copiar dados de SQL Server ou de um Azure SQL Database para o Azure SQL Data Warehouse, se a tabela não existir no repositório de destino, Data Factory poderá criar automaticamente a tabela no SQL Data Warehouse usando o esquema da tabela no armazenamento de dados de origem. Consulte [criação de tabela automática](#auto-table-creation) para obter detalhes.

## <a name="supported-authentication-type"></a>Tipo de autenticação com suporte
O conector de SQL Data Warehouse do Azure dá suporte à autenticação básica.

## <a name="getting-started"></a>Introdução
Você pode criar um pipeline com uma atividade de cópia que move dados de/para um SQL Data Warehouse do Azure usando diferentes ferramentas/APIs.

A maneira mais fácil de criar um pipeline que copia dados de/para o Azure SQL Data Warehouse é usar o assistente de cópia de dados. Consulte [tutorial: carregar dados em SQL data warehouse com o data Factory](../../sql-data-warehouse/sql-data-warehouse-load-with-data-factory.md) para obter uma explicação rápida sobre como criar um pipeline usando o assistente para copiar dados.

Você também pode usar as seguintes ferramentas para criar um pipeline: **Visual Studio**, **Azure PowerShell**, **modelo de Azure Resource Manager**, **API .net**e **API REST**. Confira o [tutorial de atividade de cópia](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) para obter instruções detalhadas para criar um pipeline com uma atividade de cópia.

Se você usar as ferramentas ou APIs, execute as seguintes etapas para criar um pipeline que move dados de um armazenamento de dados de origem para um armazenamento de dados de coletor:

1. Crie um **Data Factory**. Um data factory pode conter um ou mais pipelines. 
2. Crie **Serviços vinculados** para vincular armazenamentos de dados de entrada e saída ao seu data Factory. Por exemplo, se você estiver copiando dados de um armazenamento de BLOBs do Azure para um Azure SQL data warehouse, crie dois serviços vinculados para vincular sua conta de armazenamento do Azure e o SQL do Azure data warehouse ao seu data factory. Para propriedades do serviço vinculado que são específicas para SQL Data Warehouse do Azure, consulte a seção [Propriedades do serviço vinculado](#linked-service-properties) . 
3. Crie **conjuntos** de dados para representar o dado de entrada e saída para a operação de cópia. No exemplo mencionado na última etapa, você cria um conjunto de dados para especificar o contêiner de BLOBs e a pasta que contém o dado de entrada. E, você cria outro conjunto de dados para especificar a tabela no Azure SQL data warehouse que contém os dados copiados do armazenamento de BLOBs. Para propriedades de conjunto de informações específicas para SQL Data Warehouse do Azure, consulte a seção [Propriedades do conjunto](#dataset-properties) de informações.
4. Crie um **pipeline** com uma atividade de cópia que usa um conjunto de dados como uma entrada e um conjunto como uma saída. No exemplo mencionado anteriormente, você usa o Blobname como uma origem e SqlDWSink como um coletor para a atividade de cópia. Da mesma forma, se você estiver copiando do Azure SQL Data Warehouse para o armazenamento de BLOBs do Azure, use SqlDWSource e BlobSink na atividade de cópia. Para propriedades da atividade de cópia que são específicas para SQL Data Warehouse do Azure, consulte a seção [Propriedades da atividade de cópia](#copy-activity-properties) . Para obter detalhes sobre como usar um armazenamento de dados como uma origem ou um coletor, clique no link na seção anterior para seu armazenamento de dados.

Quando você usa o assistente, as definições de JSON para essas entidades de Data Factory (serviços vinculados, conjuntos de valores e o Pipeline) são criadas automaticamente para você. Ao usar ferramentas/APIs (exceto a API .NET), você define essas entidades de Data Factory usando o formato JSON. Para obter exemplos com definições de JSON para Data Factory entidades usadas para copiar dados de/para uma SQL Data Warehouse do Azure, confira a seção [exemplos de JSON](#json-examples-for-copying-data-to-and-from-sql-data-warehouse) deste artigo.

As seções a seguir fornecem detalhes sobre as propriedades JSON que são usadas para definir Data Factory entidades específicas para o SQL Data Warehouse do Azure:

## <a name="linked-service-properties"></a>Propriedades do serviço vinculado
A tabela a seguir fornece a descrição para elementos JSON específicos para o serviço vinculado do Azure SQL Data Warehouse.

| Propriedade | Descrição | Necessário |
| --- | --- | --- |
| tipo |A propriedade Type deve ser definida como: **AzureSqlDW** |Sim |
| connectionString |Especifique as informações necessárias para se conectar à instância de SQL Data Warehouse do Azure para a propriedade connectionString. Somente a autenticação básica tem suporte. |Sim |

> [!IMPORTANT]
> Configure o [Firewall do banco de dados SQL do Azure](https://msdn.microsoft.com/library/azure/ee621782.aspx#ConnectingFromAzure) e o servidor de banco de dados para [permitir que os serviços do Azure acessem o servidor](https://msdn.microsoft.com/library/azure/ee621782.aspx#ConnectingFromAzure). Além disso, se você estiver copiando dados para o Azure SQL Data Warehouse de fora do Azure, incluindo fontes de dados locais com o gateway de data factory, configure o intervalo de endereços IP apropriado para o computador que está enviando dados para o Azure SQL Data Warehouse.

## <a name="dataset-properties"></a>Propriedades de DataSet
Para obter uma lista completa das seções & propriedades disponíveis para definir os conjuntos de valores, consulte o artigo [criando conjuntos](data-factory-create-datasets.md) de itens. As seções como estrutura, disponibilidade e política de um conjunto de dados JSON são semelhantes para todos os tipos de conjunto de dados (SQL do Azure, BLOB do Azure, tabela do Azure, etc.).

A seção typeproperties é diferente para cada tipo de conjunto de dados e fornece informações sobre o local dos dados no repositório de dados. A seção **typeproperties** do conjunto de um do tipo **AzureSqlDWTable** tem as seguintes propriedades:

| Propriedade | Descrição | Necessário |
| --- | --- | --- |
| tableName |Nome da tabela ou exibição no banco de dados SQL Data Warehouse do Azure ao qual o serviço vinculado se refere. |Sim |

## <a name="copy-activity-properties"></a>Propriedades da atividade Copy
Para obter uma lista completa das seções & propriedades disponíveis para definir as atividades, consulte o artigo [criando pipelines](data-factory-create-pipelines.md) . Propriedades como nome, descrição, tabelas de entrada e saída e política estão disponíveis para todos os tipos de atividades.

> [!NOTE]
> A atividade de cópia usa apenas uma entrada e produz apenas uma saída.

Enquanto que as propriedades disponíveis na seção typeproperties da atividade variam de acordo com cada tipo de atividade. Para a atividade de cópia, elas variam de acordo com os tipos de fontes e coletores.

### <a name="sqldwsource"></a>SqlDWSource
Quando a fonte é do tipo **SqlDWSource**, as seguintes propriedades estão disponíveis na seção **typeproperties** :

| Propriedade | Descrição | Valores permitidos | Necessário |
| --- | --- | --- | --- |
| sqlReaderQuery |Use a consulta personalizada para ler os dados. |Cadeia de caracteres de consulta SQL. Por exemplo: selecione * em MyTable. |Não |
| sqlReaderStoredProcedureName |Nome do procedimento armazenado que lê os dados da tabela de origem. |Nome do procedimento armazenado. A última instrução SQL deve ser uma instrução SELECT no procedimento armazenado. |Não |
| storedProcedureParameters |Parâmetros para o procedimento armazenado. |Pares de nome/valor. Os nomes e maiúsculas e minúsculas dos parâmetros devem corresponder aos nomes e maiúsculas e minúsculas dos parâmetros do procedimento armazenado. |Não |

Se o **sqlReaderQuery** for especificado para o SqlDWSource, a atividade de cópia executará essa consulta na fonte de SQL data warehouse do Azure para obter os dados.

Como alternativa, você pode especificar um procedimento armazenado especificando o **sqlReaderStoredProcedureName** e **storedprocedureparameters** (se o procedimento armazenado usar parâmetros).

Se você não especificar sqlReaderQuery ou sqlReaderStoredProcedureName, as colunas definidas na seção estrutura do conjunto de dados JSON serão usadas para criar uma consulta a ser executada no SQL Data Warehouse do Azure. Exemplo: `select column1, column2 from mytable`. Se a definição do conjunto de dados não tiver a estrutura, todas as colunas serão selecionadas da tabela.

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
O **SqlDWSink** dá suporte às seguintes propriedades:

| Propriedade | Descrição | Valores permitidos | Necessário |
| --- | --- | --- | --- |
| sqlWriterCleanupScript |Especifique uma consulta para a atividade de cópia a ser executada, de modo que os dados de uma fatia específica sejam limpos. Para obter detalhes, consulte a [seção de repetição](#repeatability-during-copy). |Uma instrução de consulta. |Não |
| allowPolyBase |Indica se o polybase (quando aplicável) deve ser usado em vez do mecanismo de BULKINSERT. <br/><br/> **Usar o polybase é a maneira recomendada para carregar dados em SQL Data Warehouse.** Confira [a seção usar o polybase para carregar dados no Azure SQL data warehouse](#use-polybase-to-load-data-into-azure-sql-data-warehouse) para obter restrições e detalhes. |Verdadeiro <br/>False (padrão) |Não |
| polyBaseSettings |Um grupo de propriedades que pode ser especificado quando a propriedade **allowPolybase** é definida como **true**. |&nbsp; |Não |
| rejeitarvalue |Especifica o número ou o percentual de linhas que podem ser rejeitadas antes da falha da consulta. <br/><br/>Saiba mais sobre as opções de rejeição do polybase na seção **argumentos** do tópico [criar tabela externa (Transact-SQL)](https://msdn.microsoft.com/library/dn935021.aspx) . |0 (padrão), 1, 2,... |Não |
| rejectType |Especifica se a opção rejeiçãovalue é especificada como um valor literal ou uma porcentagem. |Valor (padrão), percentual |Não |
| rejectSampleValue |Determina o número de linhas a serem recuperadas antes que o polybase recalcule a porcentagem de linhas rejeitadas. |1, 2,... |Sim, se **rejeitátype** for **percentual** |
| useTypeDefault |Especifica como tratar valores ausentes em arquivos de texto delimitados quando o polybase recupera dados do arquivo de texto.<br/><br/>Saiba mais sobre essa propriedade na seção argumentos em [criar formato de arquivo externo (Transact-SQL)](https://msdn.microsoft.com/library/dn935026.aspx). |True, false (padrão) |Não |
| writeBatchSize |Insere dados na tabela SQL quando o tamanho do buffer atinge writeBatchSize |Inteiro (número de linhas) |Não (padrão: 10000) |
| writeBatchTimeout |Tempo de espera para que a operação de inserção em lote seja concluída antes de atingir o tempo limite. |período<br/><br/> Exemplo: "00:30:00" (30 minutos). |Não |

#### <a name="sqldwsink-example"></a>Exemplo de SqlDWSink

```JSON
"sink": {
    "type": "SqlDWSink",
    "allowPolyBase": true
}
```

## <a name="use-polybase-to-load-data-into-azure-sql-data-warehouse"></a>Use o polybase para carregar dados no Azure SQL Data Warehouse
Usar o **[polybase](https://docs.microsoft.com/sql/relational-databases/polybase/polybase-guide)** é uma maneira eficiente de carregar grandes quantidades de dados no Azure SQL data warehouse com alta taxa de transferência. Você pode ver um grande lucro na taxa de transferência usando o polybase em vez do mecanismo de BULKINSERT padrão. Consulte o [número de referência de desempenho de cópia](data-factory-copy-activity-performance.md#performance-reference) com comparação detalhada. Para obter instruções com um caso de uso, consulte [carregar 1 TB no Azure SQL data warehouse menos de 15 minutos com Azure data Factory](data-factory-load-sql-data-warehouse.md).

* Se os dados de origem estiverem no **blob do Azure ou Azure data Lake Store**e o formato for compatível com o polybase, você poderá copiar diretamente para o Azure SQL data warehouse usando o polybase. Consulte **[cópia direta usando o polybase](#direct-copy-using-polybase)** com detalhes.
* Se o formato e o armazenamento de dados de origem não forem originalmente suportados pelo polybase, você poderá usar a **[cópia em etapas usando](#staged-copy-using-polybase)** o recurso polybase. Ele também fornece uma melhor taxa de transferência convertendo automaticamente os dados em formato compatível com o polybase e armazenando os dados no armazenamento de BLOBs do Azure. Em seguida, ele carrega dados em SQL Data Warehouse.

Defina a propriedade `allowPolyBase` como **true** , conforme mostrado no exemplo a seguir para Azure data Factory usar o polybase para copiar dados para o Azure SQL data warehouse. Ao definir allowPolyBase como true, você pode especificar propriedades específicas do polybase usando o grupo de propriedades `polyBaseSettings`. consulte a seção [SqlDWSink](#sqldwsink) para obter detalhes sobre as propriedades que você pode usar com polyBaseSettings.

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

### <a name="direct-copy-using-polybase"></a>Cópia direta usando o polybase
SQL Data Warehouse polybase dá suporte direto ao blob do Azure e Azure Data Lake Store (usando a entidade de serviço) como origem e com requisitos de formato de arquivo específico. Se os dados de origem atenderem aos critérios descritos nesta seção, você poderá copiar diretamente do armazenamento de dados de origem para o Azure SQL Data Warehouse usando o polybase. Caso contrário, você pode usar a [cópia em etapas usando o polybase](#staged-copy-using-polybase).

> [!TIP]
> Para copiar dados de Data Lake Store para SQL Data Warehouse com eficiência, saiba mais de [Azure data Factory torna ainda mais fácil e conveniente descobrir informações de dados ao usar data Lake Store com SQL data warehouse](https://blogs.msdn.microsoft.com/azuredatalake/2017/04/08/azure-data-factory-makes-it-even-easier-and-convenient-to-uncover-insights-from-data-when-using-data-lake-store-with-sql-data-warehouse/).

Se os requisitos não forem atendidos, Azure Data Factory verificará as configurações e voltará automaticamente para o mecanismo de BULKINSERT para a movimentação de dados.

1. O **serviço vinculado de origem** é do tipo: **AzureStorage** ou **AzureDataLakeStore com autenticação de entidade de serviço**.
2. O **conjunto de dados de entrada** é do tipo: **AzureBlob** ou **AzureDataLakeStore**, e o tipo de formato em Propriedades de `type` é **OrcFormat**, **ParquetFormat**ou **TextFormat** com as seguintes configurações:

   1. `rowDelimiter` deve ser **\n**.
   2. `nullValue` é definido como uma **cadeia de caracteres vazia** ("") ou `treatEmptyAsNull` é definido como **true**.
   3. `encodingName` é definido como **UTF-8**, que é o valor **padrão** .
   4. `escapeChar`, `quoteChar`, `firstRowAsHeader`e `skipLineCount` não são especificados.
   5. `compression` não pode ser **nenhuma compactação**, **gzip**ou **deflate**.

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

3. Não há nenhuma configuração de `skipHeaderLineCount` em **blob** ou **AzureDataLakeStore** para a atividade de cópia no pipeline.
4. Não há nenhuma configuração de `sliceIdentifierColumnName` em **SqlDWSink** para a atividade de cópia no pipeline. (O polybase garante que todos os dados sejam atualizados ou que nada seja atualizado em uma única execução. Para obter a capacidade de **repetição**, você pode usar `sqlWriterCleanupScript`).
5. Não há `columnMapping` sendo usadas na atividade de cópia associada.

### <a name="staged-copy-using-polybase"></a>Cópia em etapas usando o polybase
Quando os dados de origem não atendem aos critérios introduzidos na seção anterior, você pode habilitar a cópia de dados por meio de um armazenamento de BLOBs do Azure de preparo provisório (não pode ser armazenamento Premium). Nesse caso, Azure Data Factory executa automaticamente as transformações nos dados para atender aos requisitos de formato de dados do polybase, em seguida, usa o polybase para carregar dados no SQL Data Warehouse e, na última vez, limpa os dados temporários do armazenamento de BLOBs. Consulte [cópia em etapas](data-factory-copy-activity-performance.md#staged-copy) para obter detalhes sobre como copiar dados por meio de um blob do Azure de preparo funciona em geral.

> [!NOTE]
> Ao copiar dados de um armazenamento de dados local para o Azure SQL Data Warehouse usando o polybase e o preparo, se a versão do gateway de Gerenciamento de Dados estiver abaixo de 2,4, o JRE (Java Runtime Environment) será necessário no computador do gateway que é usado para transformar sua origem dados em formato adequado. Sugira que você atualize seu gateway para a versão mais recente para evitar essa dependência.
>

Para usar esse recurso, crie um [serviço vinculado do armazenamento do Azure](data-factory-azure-blob-connector.md#azure-storage-linked-service) que se refere à conta de armazenamento do Azure que tem o armazenamento de BLOBs provisório e, em seguida, especifique as propriedades `enableStaging` e `stagingSettings` para a atividade de cópia, conforme mostrado no código a seguir:

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

## <a name="best-practices-when-using-polybase"></a>Práticas recomendadas ao usar o polybase
As seções a seguir fornecem práticas recomendadas adicionais para aquelas que são mencionadas nas [práticas recomendadas para o Azure SQL data warehouse](../../sql-data-warehouse/sql-data-warehouse-best-practices.md).

### <a name="required-database-permission"></a>Permissão de banco de dados necessária
Para usar o polybase, é necessário que o usuário que está sendo usado para carregar dados no SQL Data Warehouse tenha a [permissão de "controle"](https://msdn.microsoft.com/library/ms191291.aspx) no banco de dados de destino. Uma maneira de conseguir isso é adicionar esse usuário como um membro da função "db_owner". Saiba como fazer isso seguindo [esta seção](../../sql-data-warehouse/sql-data-warehouse-overview-manage-security.md#authorization).

### <a name="row-size-and-data-type-limitation"></a>Limitação de tamanho de linha e de tipo de dados
As cargas do polybase são limitadas ao carregamento de linhas menores que **1 MB** e não podem ser carregadas em VARCHR (max), nvarchar (max) ou varbinary (max). Consulte [aqui](../../sql-data-warehouse/sql-data-warehouse-service-capacity-limits.md#loads).

Se você tiver dados de origem com linhas de tamanho maior que 1 MB, talvez você queira dividir as tabelas de origem verticalmente em várias pequenas em que o maior tamanho de linha de cada uma delas não exceda o limite. As tabelas menores podem então ser carregadas usando o polybase e mescladas em conjunto no Azure SQL Data Warehouse.

### <a name="sql-data-warehouse-resource-class"></a>Classe de recurso SQL Data Warehouse
Para obter a melhor taxa de transferência possível, considere atribuir uma classe de recurso maior ao usuário que está sendo usado para carregar dados em SQL Data Warehouse por meio do polybase. Saiba como fazer isso seguindo [alterar um exemplo de classe de recurso de usuário](../../sql-data-warehouse/sql-data-warehouse-develop-concurrency.md).

### <a name="tablename-in-azure-sql-data-warehouse"></a>TableName no Azure SQL Data Warehouse
A tabela a seguir fornece exemplos de como especificar a propriedade **TableName** no DataSet JSON para várias combinações de esquema e nome de tabela.

| Esquema do BD | Nome da tabela | Propriedade JSON TableName |
| --- | --- | --- |
| dbo |MyTable |MyTable ou dbo. MyTable ou [dbo]. MyTable |
| dbo1 |MyTable |dbo1. MyTable ou [dbo1]. MyTable |
| dbo |Minha. tabela |[My. Table] ou [dbo]. [Minha. tabela] |
| dbo1 |Minha. tabela |[dbo1]. [Minha. tabela] |

Se você vir o erro a seguir, pode ser um problema com o valor especificado para a Propriedade TableName. Consulte a tabela para obter a maneira correta de especificar valores para a propriedade JSON TableName.

```
Type=System.Data.SqlClient.SqlException,Message=Invalid object name 'stg.Account_test'.,Source=.Net SqlClient Data Provider
```

### <a name="columns-with-default-values"></a>Colunas com valores padrão
Atualmente, o recurso polybase no Data Factory aceita apenas o mesmo número de colunas que na tabela de destino. Digamos que você tenha uma tabela com quatro colunas e uma delas seja definida com um valor padrão. Os dados de entrada ainda devem conter quatro colunas. Fornecer um conjunto de dados de entrada de 3 colunas produziria um erro semelhante à seguinte mensagem:

```
All columns of the table must be specified in the INSERT BULK statement.
```
O valor nulo é uma forma especial de valor padrão. Se a coluna for anulável, os dados de entrada (no BLOB) para essa coluna poderão estar vazios (não podem estar ausentes no DataSet de entrada). O polybase insere NULL para eles no SQL Data Warehouse do Azure.

## <a name="auto-table-creation"></a>Criação automática de tabela
Se você estiver usando o assistente de cópia para copiar dados de SQL Server ou de um Azure SQL Database para o Azure SQL Data Warehouse e a tabela que corresponde à tabela de origem não existir no repositório de destino, Data Factory poderá criar a tabela automaticamente no data warehouse por u o esquema da tabela de origem.

Data Factory cria a tabela no repositório de destino com o mesmo nome de tabela no armazenamento de dados de origem. Os tipos de dados para colunas são escolhidos com base no mapeamento de tipo a seguir. Se necessário, ele executa conversões de tipo para corrigir quaisquer incompatibilidades entre os repositórios de origem e de destino. Ele também usa a distribuição de tabela Round Robin.

| Tipo de coluna do banco de dados SQL de origem | Tipo de coluna do SQL DW de destino (limitação de tamanho) |
| --- | --- |
| inteiro | inteiro |
| BigInt | BigInt |
| SmallInt | SmallInt |
| TinyInt | TinyInt |
| parte | parte |
| Vírgula | Vírgula |
| numeric | Vírgula |
| Barra | Barra |
| gastar | gastar |
| Real | Real |
| SmallMoney | SmallMoney |
| binário | binário |
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
| º | º |
| NChar | NChar |
| VarChar | VarChar (até 8000) |
| NVarChar | NVarChar (até 4000) |
| XML | Varchar (até 8000) |

[!INCLUDE [data-factory-type-repeatability-for-sql-sources](../../../includes/data-factory-type-repeatability-for-sql-sources.md)]

## <a name="type-mapping-for-azure-sql-data-warehouse"></a>Mapeamento de tipo para SQL Data Warehouse do Azure
Conforme mencionado no artigo sobre as [atividades de movimentação de dados](data-factory-data-movement-activities.md) , a atividade de cópia executa conversões automáticas dos tipos de origem nos tipos de coletor com a seguinte abordagem de duas etapas:

1. Converter de tipos de origem nativos em tipo .NET
2. Converter do tipo .NET para o tipo de coletor nativo

Ao mover dados para & do SQL Data Warehouse do Azure, os seguintes mapeamentos são usados do tipo SQL para o tipo .NET e vice-versa.

O mapeamento é o mesmo que o [SQL Server mapeamento de tipo de dados para ADO.net](https://msdn.microsoft.com/library/cc716729.aspx).

| Tipo de Mecanismo de Banco de Dados de SQL Server | Tipo de .NET Framework |
| --- | --- |
| bigint |Int64 |
| binário |Byte [] |
| parte |Booleano |
| º |Cadeia de caracteres, Char [] |
| date |DateTime |
| Horário |DateTime |
| datetime2 |DateTime |
| DateTimeOffset |DateTimeOffset |
| Vírgula |Vírgula |
| Atributo FILESTREAM (varbinary (max)) |Byte [] |
| Barra |Clique |
| image |Byte [] |
| int |Int32 |
| gastar |Vírgula |
| nchar |Cadeia de caracteres, Char [] |
| ntext |Cadeia de caracteres, Char [] |
| numeric |Vírgula |
| nvarchar |Cadeia de caracteres, Char [] |
| foto |Único |
| rowversion |Byte [] |
| smalldatetime |DateTime |
| smallint |Int16 |
| smallmoney |Vírgula |
| sql_variant |Objeto |
| texto |Cadeia de caracteres, Char [] |
| hora |Período |
| carimbo de data/hora |Byte [] |
| tinyint |Minuciosa |
| uniqueidentifier |GUID |
| varbinary |Byte [] |
| varchar |Cadeia de caracteres, Char [] |
| xml |XML |

Você também pode mapear colunas do conjunto de fonte de origem para colunas do conjunto de coleta da atividade de cópia. Para obter detalhes, consulte [mapeando colunas do conjunto de informações em Azure data Factory](data-factory-map-columns.md).

## <a name="json-examples-for-copying-data-to-and-from-sql-data-warehouse"></a>Exemplos de JSON para copiar dados de e para SQL Data Warehouse
Os exemplos a seguir fornecem exemplos de definições de JSON que você pode usar para criar um pipeline usando o [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) ou [Azure PowerShell](data-factory-copy-activity-tutorial-using-powershell.md). Eles mostram como copiar dados de e para o Azure SQL Data Warehouse e o armazenamento de BLOBs do Azure. No entanto, os dados podem ser copiados **diretamente** de qualquer uma das fontes para qualquer um dos coletores declarados [aqui](data-factory-data-movement-activities.md#supported-data-stores-and-formats) usando a atividade de cópia no Azure data Factory.

### <a name="example-copy-data-from-azure-sql-data-warehouse-to-azure-blob"></a>Exemplo: copiar dados do Azure SQL Data Warehouse para o blob do Azure
O exemplo define as seguintes entidades de Data Factory:

1. Um serviço vinculado do tipo [AzureSqlDW](#linked-service-properties).
2. Um serviço vinculado do tipo [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties).
3. Um [conjunto](data-factory-create-datasets.md) de dados de entrada do tipo [AzureSqlDWTable](#dataset-properties).
4. Um [conjunto](data-factory-create-datasets.md) de uma saída do tipo [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties).
5. Um [pipeline](data-factory-create-pipelines.md) com atividade de cópia que usa [SqlDWSource](#copy-activity-properties) e [BlobSink](data-factory-azure-blob-connector.md#copy-activity-properties).

O exemplo copia dados da série temporal (por hora, diariamente, etc.) de uma tabela no banco de SQL Data Warehouse do Azure para um blob a cada hora. As propriedades JSON usadas nesses exemplos são descritas em seções após os exemplos.

**Serviço vinculado do Azure SQL Data Warehouse:**

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
**Serviço vinculado do armazenamento de BLOBs do Azure:**

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
**Conjunto de dados de entrada do Azure SQL Data Warehouse:**

O exemplo supõe que você criou uma tabela "MyTable" no Azure SQL Data Warehouse e que ela contém uma coluna chamada "timestampcolumn" para dados de série temporal.

A configuração "external": "true" informa ao serviço de Data Factory que o conjunto de os é externo ao data factory e não é produzido por uma atividade no data factory.

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
**Conjunto de resultados de saída de blob do Azure:**

Os dados são gravados em um novo BLOB a cada hora (frequência: hora, intervalo: 1). O caminho da pasta para o blob é avaliado dinamicamente com base na hora de início da fatia que está sendo processada. O caminho da pasta usa as partes ano, mês, dia e horas da hora de início.

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

**Atividade de cópia em um pipeline com SqlDWSource e BlobSink:**

O pipeline contém uma atividade de cópia configurada para usar os conjuntos de dados de entrada e saída e está agendada para ser executada a cada hora. Na definição de JSON do pipeline, o tipo de **origem** é definido como **SqlDWSource** e o tipo de **coletor** está definido como **BlobSink**. A consulta SQL especificada para a propriedade **SqlReaderQuery** seleciona os dados na última hora a serem copiados.

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
> No exemplo, **sqlReaderQuery** é especificado para o SqlDWSource. A atividade de cópia executa essa consulta na fonte de SQL Data Warehouse do Azure para obter os dados.
>
> Como alternativa, você pode especificar um procedimento armazenado especificando o **sqlReaderStoredProcedureName** e **storedprocedureparameters** (se o procedimento armazenado usar parâmetros).
>
> Se você não especificar sqlReaderQuery ou sqlReaderStoredProcedureName, as colunas definidas na seção estrutura do conjunto de dados JSON serão usadas para criar uma consulta (selecione column1, Coluna2 de mytable) para executar na SQL Data Warehouse do Azure. Se a definição do conjunto de dados não tiver a estrutura, todas as colunas serão selecionadas da tabela.
>
>

### <a name="example-copy-data-from-azure-blob-to-azure-sql-data-warehouse"></a>Exemplo: copiar dados do blob do Azure para o Azure SQL Data Warehouse
O exemplo define as seguintes entidades de Data Factory:

1. Um serviço vinculado do tipo [AzureSqlDW](#linked-service-properties).
2. Um serviço vinculado do tipo [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties).
3. Um [conjunto](data-factory-create-datasets.md) de dados de entrada do tipo [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties).
4. Um [conjunto](data-factory-create-datasets.md) de uma saída do tipo [AzureSqlDWTable](#dataset-properties).
5. Um [pipeline](data-factory-create-pipelines.md) com atividade de cópia que usa [blobname](data-factory-azure-blob-connector.md#copy-activity-properties) e [SqlDWSink](#copy-activity-properties).

O exemplo copia dados de série temporal (por hora, diariamente, etc.) do blob do Azure para uma tabela no Azure SQL Data Warehouse banco de dados a cada hora. As propriedades JSON usadas nesses exemplos são descritas em seções após os exemplos.

**Serviço vinculado do Azure SQL Data Warehouse:**

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
**Serviço vinculado do armazenamento de BLOBs do Azure:**

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
**Conjunto de dados de entrada de blob do Azure:**

Os dados são coletados de um novo BLOB a cada hora (frequência: hora, intervalo: 1). O caminho da pasta e o nome do arquivo para o blob são avaliados dinamicamente com base na hora de início da fatia que está sendo processada. O caminho da pasta usa a parte de ano, mês e dia da hora de início e o nome do arquivo usa a parte de hora da hora de início. a configuração "external": "true" informa ao serviço de Data Factory que essa tabela é externa à data factory e não é produzida por uma atividade no data factory.

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
**Conjunto de SQL Data Warehouse de saída do Azure:**

O exemplo copia dados para uma tabela chamada "MyTable" no Azure SQL Data Warehouse. Crie a tabela no Azure SQL Data Warehouse com o mesmo número de colunas que você espera que o arquivo CSV de blob contenha. Novas linhas são adicionadas à tabela a cada hora.

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
**Atividade de cópia em um pipeline com Blobname e SqlDWSink:**

O pipeline contém uma atividade de cópia configurada para usar os conjuntos de dados de entrada e saída e está agendada para ser executada a cada hora. Na definição de JSON do pipeline, o tipo de **origem** está definido como **blobname** e o tipo de **coletor** está definido como **SqlDWSink**.

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
Para obter instruções, confira [carregar 1 TB no Azure SQL data warehouse em 15 minutos com Azure data Factory](data-factory-load-sql-data-warehouse.md) e [carregar dados com Azure data Factory](../../sql-data-warehouse/sql-data-warehouse-get-started-load-with-azure-data-factory.md) artigo na documentação do SQL data warehouse do Azure.

## <a name="performance-and-tuning"></a>Desempenho e ajuste
Consulte [Guia de ajuste do desempenho de atividade de cópia &](data-factory-copy-activity-performance.md) para saber mais sobre os principais fatores que afetam o desempenho da movimentação de dados (atividade de cópia) no Azure data Factory e várias maneiras de otimizá-lo.
