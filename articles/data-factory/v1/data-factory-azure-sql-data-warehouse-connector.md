---
title: Copiar dados para/a partir do Armazém de Dados Azure SQL
description: Saiba como copiar dados de/para o Armazém de Dados Azure SQL utilizando a Azure Data Factory
services: data-factory
documentationcenter: ''
author: linda33wj
manager: shwang
ms.assetid: d90fa9bd-4b79-458a-8d40-e896835cfd4a
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 01/10/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: 4335763269f4a39b4893d9022f4789296b178e92
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "81419328"
---
# <a name="copy-data-to-and-from-azure-sql-data-warehouse-using-azure-data-factory"></a>Copiar dados de e para o Armazém de Dados Azure SQL utilizando a Azure Data Factory
> [!div class="op_single_selector" title1="Selecione a versão do serviço Data Factory que está a utilizar:"]
> * [Versão 1](data-factory-azure-sql-data-warehouse-connector.md)
> * [Versão 2 (versão atual)](../connector-azure-sql-data-warehouse.md)

> [!NOTE]
> Este artigo aplica-se à versão 1 do Data Factory. Se estiver a utilizar a versão atual do serviço Data Factory, consulte o [conector Azure SQL Data Warehouse em V2](../connector-azure-sql-data-warehouse.md).

Este artigo explica como utilizar a Atividade de Cópia na Fábrica de Dados Azure para mover dados de/para o Azure SQL Data Warehouse. Baseia-se no artigo Atividades do Movimento de [Dados,](data-factory-data-movement-activities.md) que apresenta uma visão geral do movimento de dados com a atividade de cópia.

> [!TIP]
> Para obter o melhor desempenho, utilize a PolyBase para carregar dados no Azure SQL Data Warehouse. A [Utilização polyBase para carregar dados na secção Azure SQL Data Warehouse](data-factory-azure-sql-data-warehouse-connector.md#use-polybase-to-load-data-into-azure-sql-data-warehouse) tem detalhes. Para um passeio com uma caixa de utilização, consulte a Carregue 1 TB no Armazém de [Dados Azure SQL em menos de 15 minutos com](data-factory-load-sql-data-warehouse.md)a Azure Data Factory .

## <a name="supported-scenarios"></a>Cenários suportados
Pode copiar dados **do Azure SQL Data Warehouse** para as seguintes lojas de dados:

[!INCLUDE [data-factory-supported-sinks](../../../includes/data-factory-supported-sinks.md)]

Pode copiar dados das seguintes lojas de dados para o Armazém de **Dados Azure SQL:**

[!INCLUDE [data-factory-supported-sources](../../../includes/data-factory-supported-sources.md)]

> [!TIP]
> Ao copiar dados do SQL Server ou da Base de Dados Azure SQL para o Azure SQL Data Warehouse, se a tabela não existir na loja de destino, a Data Factory pode criar automaticamente a tabela no SQL Data Warehouse utilizando o esquema da tabela na loja de dados fonte. Consulte a [criação de mesa auto](#auto-table-creation) para mais detalhes.

## <a name="supported-authentication-type"></a>Tipo de autenticação suportada
O conector Azure SQL Data Warehouse suporta a autenticação básica.

## <a name="getting-started"></a>Introdução
Pode criar um pipeline com uma atividade de cópia que move dados de/para um Armazém de Dados Azure SQL utilizando diferentes ferramentas/APIs.

A forma mais fácil de criar um pipeline que copie dados de/para o Azure SQL Data Warehouse é utilizar o assistente de dados Copy. Consulte Tutorial: Carregue os dados no Armazém de [Dados SQL com data Factory](../../sql-data-warehouse/sql-data-warehouse-load-with-data-factory.md) para uma rápida passagem pela criação de um pipeline utilizando o assistente de dados Copy.

Também pode utilizar as seguintes ferramentas para criar um pipeline: **Estúdio Visual,** **Azure PowerShell,** **Modelo de Gestor de Recursos Azure,** **.NET API**e **REST API**. Consulte o tutorial de [atividade de cópia](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) para obter instruções passo a passo para criar um pipeline com uma atividade de cópia.

Quer utilize as ferramentas ou APIs, executa os seguintes passos para criar um pipeline que transfere dados de uma loja de dados de origem para uma loja de dados de sumidouro:

1. Criar uma fábrica de **dados.** Uma fábrica de dados pode conter um ou mais oleodutos. 
2. Crie **serviços ligados** para ligar as lojas de dados de entrada e saída à sua fábrica de dados. Por exemplo, se estiver a copiar dados de um armazenamento de blob Azure para um armazém de dados Azure SQL, cria dois serviços ligados para ligar a sua conta de armazenamento Azure e o armazém de dados Azure SQL à sua fábrica de dados. Para propriedades de serviço seleções ligadas específicas ao Azure SQL Data Warehouse, consulte a secção de propriedades de [serviço seleto.](#linked-service-properties) 
3. Crie **conjuntos** de dados para representar dados de entrada e saída para a operação de cópia. No exemplo mencionado no último passo, cria-se um conjunto de dados para especificar o recipiente e a pasta blob que contém os dados de entrada. E cria outro conjunto de dados para especificar a tabela no armazém de dados Azure SQL que detém os dados copiados do armazenamento de blob. Para propriedades de conjunto de dados específicas do Armazém de Dados Azure SQL, consulte a secção de propriedades do conjunto de [dados.](#dataset-properties)
4. Crie um **pipeline** com uma atividade de cópia que tome um conjunto de dados como entrada e um conjunto de dados como uma saída. No exemplo mencionado anteriormente, você usa blobSource como fonte e SqlDWSink como um lavatório para a atividade de cópia. Da mesma forma, se estiver a copiar do Armazém de Dados Azure SQL para o Armazenamento De Blob Azure, utiliza o SqlDWSource e o BlobSink na atividade da cópia. Para propriedades de copy activity específicas para O Armazém de Dados Azure SQL, consulte a secção de propriedades de atividade de [cópia.](#copy-activity-properties) Para obter mais informações sobre como utilizar uma loja de dados como fonte ou pia, clique no link na secção anterior para a sua loja de dados.

Quando utiliza o assistente, as definições jSON para estas entidades da Fábrica de Dados (serviços ligados, conjuntos de dados e o pipeline) são automaticamente criadas para si. Quando utiliza ferramentas/APIs (exceto .NET API), define estas entidades da Fábrica de Dados utilizando o formato JSON. Para amostras com definições JSON para entidades da Fábrica de Dados que são usadas para copiar dados de/para um Armazém de Dados Azure SQL, consulte a secção de [exemplos jSON](#json-examples-for-copying-data-to-and-from-sql-data-warehouse) deste artigo.

As seguintes secções fornecem detalhes sobre as propriedades jSON que são usadas para definir entidades da Fábrica de Dados específicas do Armazém de Dados Azure SQL:

## <a name="linked-service-properties"></a>Propriedades de serviço seletos
A tabela seguinte fornece descrição para elementos JSON específicos do serviço ligado ao Armazém de Dados Azure SQL.

| Propriedade | Descrição | Necessário |
| --- | --- | --- |
| tipo |A propriedade tipo deve ser definida para: **AzureSqlDW** |Sim |
| conexãoString |Especifique as informações necessárias para se ligar à instância do Armazém de Dados Azure SQL para a propriedade de conexãoString. Apenas a autenticação básica é suportada. |Sim |

> [!IMPORTANT]
> Configure firewall de base de [dados Azure SQL](https://msdn.microsoft.com/library/azure/ee621782.aspx#ConnectingFromAzure) e o servidor de base de dados para [permitir que os Serviços Azure acedam ao servidor](https://msdn.microsoft.com/library/azure/ee621782.aspx#ConnectingFromAzure). Além disso, se estiver a copiar dados para o Azure SQL Data Warehouse de fora do Azure, incluindo a partir de fontes de dados no local com gateway de fábrica de dados, configure a gama de endereços IP apropriado para a máquina que está a enviar dados para o Azure SQL Data Warehouse.

## <a name="dataset-properties"></a>Dataset properties (Propriedades do conjunto de dados)
Para obter uma lista completa de secções & propriedades disponíveis para definir conjuntos de dados, consulte o artigo Criação de conjuntos de [dados.](data-factory-create-datasets.md) Secções como estrutura, disponibilidade e política de um conjunto de dados JSON são semelhantes para todos os tipos de conjuntos de dados (Azure SQL, Azure blob, tabela Azure, etc.).

A secção typeProperties é diferente para cada tipo de conjunto de dados e fornece informações sobre a localização dos dados na loja de dados. A secção **TypeProperties** para o conjunto de dados do tipo **AzureSqlDWTable** tem as seguintes propriedades:

| Propriedade | Descrição | Necessário |
| --- | --- | --- |
| tableName |Nome da tabela ou vista na base de dados Azure SQL Data Warehouse a que o serviço ligado se refere. |Sim |

## <a name="copy-activity-properties"></a>Propriedades da atividade Copy
Para obter uma lista completa de secções & propriedades disponíveis para definir atividades, consulte o artigo [Creating Pipelines.](data-factory-create-pipelines.md) Propriedades como nome, descrição, tabelas de entrada e saída, e a política estão disponíveis para todos os tipos de atividades.

> [!NOTE]
> A Copy Activity leva apenas uma entrada e produz apenas uma saída.

Considerando que as propriedades disponíveis na secção typeProperties da atividade variam com cada tipo de atividade. Para a atividade de Cópia, variam dependendo dos tipos de fontes e pias.

### <a name="sqldwsource"></a>SqlDWSource
Quando a fonte é do tipo **SqlDWSource,** as seguintes propriedades estão disponíveis na secção **TypeProperties:**

| Propriedade | Descrição | Valores permitidos | Necessário |
| --- | --- | --- | --- |
| sqlReaderQuery |Use a consulta personalizada para ler dados. |Fio de consulta SQL. Por exemplo: selecione * do MyTable. |Não |
| sqlReaderStoredProcedureName |Nome do procedimento armazenado que lê os dados da tabela de origem. |Nome do procedimento armazenado. A última declaração sQL deve ser uma declaração SELECT no procedimento armazenado. |Não |
| parâmetros de procedimento saqueados |Parâmetros para o procedimento armazenado. |Nome/pares de valor. Os nomes e o invólucro dos parâmetros devem coincidir com os nomes e o invólucro dos parâmetros do procedimento armazenado. |Não |

Se o **sqlReaderQuery** for especificado para o SqlDWSource, a Atividade de Cópia executa esta consulta contra a fonte do Armazém de Dados Azure SQL para obter os dados.

Em alternativa, pode especificar um procedimento armazenado especificando o nome de **procedimento sqlReaderStoredEdE** e **os parâmetros de procedimento armazenados** (se o procedimento armazenado tiver parâmetros).

Se não especificar o sqlReaderQuery ou o sqlReaderStoredProcedureName, as colunas definidas na secção de estrutura do conjunto de dados JSON são usadas para construir uma consulta para ser executada contra o Armazém de Dados Azure SQL. Exemplo: `select column1, column2 from mytable`. Se a definição de conjunto de dados não tiver a estrutura, todas as colunas são selecionadas a partir da tabela.

#### <a name="sqldwsource-example"></a>Exemplo sqlDWSource

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
**A definição de procedimento armazenado:**

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
**A SqlDWSink** suporta as seguintes propriedades:

| Propriedade | Descrição | Valores permitidos | Necessário |
| --- | --- | --- | --- |
| sqlWriterCleanupScript |Especifique uma consulta para a Atividade de Cópia executar de tal forma que os dados de uma fatia específica são limpos. Para mais detalhes, consulte a [secção de repetibilidade](#repeatability-during-copy). |Uma declaração de consulta. |Não |
| permitir a PolyBase |Indica se deve utilizar o PolyBase (quando aplicável) em vez do mecanismo BULKINSERT. <br/><br/> **Utilizar a PolyBase é a forma recomendada de carregar dados no Armazém de Dados SQL.** Consulte [a Utilização da PolyBase para carregar dados na secção Azure SQL Data Warehouse](#use-polybase-to-load-data-into-azure-sql-data-warehouse) para obter constrangimentos e detalhes. |Verdadeiro <br/>Falso (predefinição) |Não |
| poliBaseSettings |Um grupo de propriedades que pode ser especificado quando a propriedade **permitida Polybase** é definida como **verdadeira**. |&nbsp; |Não |
| rejeitarValue |Especifica o número ou percentagem de linhas que podem ser rejeitadas antes da consulta falhar. <br/><br/>Saiba mais sobre as opções de rejeição da PolyBase na secção **argumentos** do tópico [CREATE EXTERNAL TABLE (Transact-SQL).](https://msdn.microsoft.com/library/dn935021.aspx) |0 (padrão), 1, 2, ... |Não |
| rejeitarType |Especifica se a opção rejectValue é especificada como um valor literal ou uma percentagem. |Valor (padrão), Percentagem |Não |
| rejeitarSampleValue |Determina o número de linhas a recuperar antes que a PolyBase recalcule a percentagem de linhas rejeitadas. |1, 2, ... |Sim, se **rejeitarType** é **percentual** |
| useTypeDefault |Especifica como lidar com valores em falta em ficheiros de texto delimitados quando a PolyBase recupera dados do ficheiro de texto.<br/><br/>Saiba mais sobre esta propriedade a partir da secção Argumentos em FORMATO DE [ARQUIVO EXTERNO (Transact-SQL)](https://msdn.microsoft.com/library/dn935026.aspx). |Verdade, Falso (padrão) |Não |
| escreverBatchSize |Insere os dados na tabela SQL quando o tamanho do tampão atinge o writeBatchSize |Inteiro (número de linhas) |Não (padrão: 10000) |
| escreverBatchTimeout |Aguarde o tempo para que a operação de inserção do lote esteja concluída antes de sair. |timespan<br/><br/> Exemplo: "00:30:00" (30 minutos). |Não |

#### <a name="sqldwsink-example"></a>Exemplo sqlDWSink

```JSON
"sink": {
    "type": "SqlDWSink",
    "allowPolyBase": true
}
```

## <a name="use-polybase-to-load-data-into-azure-sql-data-warehouse"></a>Utilize a PolyBase para carregar dados no Armazém de Dados Azure SQL
A utilização **[da PolyBase](https://docs.microsoft.com/sql/relational-databases/polybase/polybase-guide)** é uma forma eficiente de carregar uma grande quantidade de dados no Azure SQL Data Warehouse com alta saída. Pode ver um grande ganho na entrada utilizando a PolyBase em vez do mecanismo de bulkinsert predefinido. Consulte o número de referência de [desempenho da cópia](data-factory-copy-activity-performance.md#performance-reference) com comparação detalhada. Para um passeio com uma caixa de utilização, consulte a Carregue 1 TB no Armazém de [Dados Azure SQL em menos de 15 minutos com](data-factory-load-sql-data-warehouse.md)a Azure Data Factory .

* Se os seus dados de origem estiverem na **Azure Blob ou na Azure Data Lake Store,** e o formato for compatível com a PolyBase, pode copiar diretamente para o Azure SQL Data Warehouse utilizando a PolyBase. Consulte a cópia direta utilizando a **[PolyBase](#direct-copy-using-polybase)** com detalhes.
* Se a sua loja de dados de origem e formato não for originalmente suportado pela PolyBase, pode utilizar a Cópia Encenada utilizando a função **[PolyBase.](#staged-copy-using-polybase)** Também lhe fornece melhor a sua entrada, convertendo automaticamente os dados em formato compatível com a PolyBase e armazenando os dados no armazenamento do Azure Blob. Em seguida, carrega dados para o Armazém de Dados SQL.

Deteto **true** a `allowPolyBase` propriedade como mostrado no exemplo seguinte para a Azure Data Factory utilizar a PolyBase para copiar dados no Azure SQL Data Warehouse. Quando definir permitir a TrueBase, pode especificar propriedades `polyBaseSettings` específicas da PolyBase utilizando o grupo de propriedades. consulte a secção [SqlDWSink](#sqldwsink) para obter detalhes sobre propriedades que pode utilizar com poliBaseSettings.

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

### <a name="direct-copy-using-polybase"></a>Cópia direta usando polyBase
O SQL Data Warehouse PolyBase suporta diretamente a Azure Blob e a Azure Data Lake Store (utilizando o principal de serviço) como fonte e com requisitos específicos de formato de ficheiro. Se os seus dados de origem cumprirem os critérios descritos nesta secção, pode copiar diretamente da loja de dados de origem para o Armazém de Dados Azure SQL utilizando a PolyBase. Caso contrário, pode utilizar [a Cópia Encenada utilizando a PolyBase](#staged-copy-using-polybase).

> [!TIP]
> Para copiar dados da Data Lake Store para o SQL Data Warehouse de forma eficiente, aprenda mais com a [Azure Data Factory torna ainda mais fácil e conveniente descobrir insights a partir de dados ao utilizar data lake store com SQL Data Warehouse](https://blogs.msdn.microsoft.com/azuredatalake/2017/04/08/azure-data-factory-makes-it-even-easier-and-convenient-to-uncover-insights-from-data-when-using-data-lake-store-with-sql-data-warehouse/).

Se os requisitos não forem cumpridos, a Azure Data Factory verifica as definições e recai automaticamente para o mecanismo BULKINSERT para o movimento de dados.

1. **O serviço ligado** à fonte é de tipo: **AzureStorage** ou **AzureDataLakeStore com autenticação principal**de serviço .
2. O **conjunto** de dados de entrada é de tipo: **AzureBlob** ou `type` **AzureDataLakeStore**, e o tipo de formato em propriedades é **OrcFormat,** **ParquetFormat**ou **TextFormat** com as seguintes configurações:

   1. `rowDelimiter`deve ser **\n**.
   2. `nullValue`é definido para **cadeia** vazia `treatEmptyAsNull` (""), ou está definido como **verdadeiro**.
   3. `encodingName`é definido para **utf-8**, que é o valor **padrão.**
   4. `escapeChar``firstRowAsHeader`E `quoteChar`não `skipLineCount` estão especificados.
   5. `compression`pode não ser **compressão,** **GZip,** ou **Esvaziar**.

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

3. Não existe `skipHeaderLineCount` qualquer definição no âmbito do **BlobSource** ou **do AzureDataLakeStore** para a atividade copy no pipeline.
4. Não existe `sliceIdentifierColumnName` qualquer regulação no âmbito **do SqlDWSink** para a atividade copy no gasoduto. (A PolyBase garante que todos os dados são atualizados ou nada é atualizado numa única execução. Para alcançar a **repetível,** pode utilizar). `sqlWriterCleanupScript`
5. Não há `columnMapping` uso na atividade da Cópia associada.

### <a name="staged-copy-using-polybase"></a>Cópia Encenada usando polyBase
Quando os seus dados de origem não satisfazem os critérios introduzidos na secção anterior, pode ativar a cópia de dados através de uma encenação provisória do Armazenamento De Blob (não pode ser Armazenamento Premium). Neste caso, a Azure Data Factory realiza automaticamente transformações nos dados para satisfazer os requisitos de formato de dados da PolyBase, depois utilizar a PolyBase para carregar dados no SQL Data Warehouse e, finalmente, limpar os dados temporários a partir do armazenamento blob. Consulte a [Cópia Encenada](data-factory-copy-activity-performance.md#staged-copy) para obter detalhes sobre como a cópia dos dados através de uma encenação do Azure Blob funciona em geral.

> [!NOTE]
> Ao copiar dados de uma loja de dados no local para o Azure SQL Data Warehouse utilizando a PolyBase e a encenação, se a sua versão Gateway de Gestão de Dados for inferior a 2.4, o JRE (Java Runtime Environment) é necessário na sua máquina de gateway que é usada para transformar os seus dados de origem em formato adequado. Sugira que atualize a sua porta de entrada para o mais tardar para evitar tal dependência.
>

Para utilizar esta funcionalidade, crie um serviço ligado ao [Armazenamento Azure](data-factory-azure-blob-connector.md#azure-storage-linked-service) que se refira `enableStaging` à `stagingSettings` Conta de Armazenamento Azure que tenha o armazenamento de blob provisório, em seguida, especifique as propriedades e propriedades para a Atividade de Cópia, como mostrado no seguinte código:

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

## <a name="best-practices-when-using-polybase"></a>Boas práticas ao utilizar a PolyBase
As seguintes secções fornecem boas práticas adicionais às que são mencionadas nas [Melhores Práticas para o Armazém de Dados Azure SQL](../../synapse-analytics/sql-data-warehouse/sql-data-warehouse-best-practices.md).

### <a name="required-database-permission"></a>Autorização de base de dados necessária
Para utilizar o PolyBase, é necessário que o utilizador seja utilizado para carregar dados no SQL Data Warehouse tem a [permissão "CONTROL"](https://msdn.microsoft.com/library/ms191291.aspx) na base de dados do alvo. Uma forma de o conseguir é adicionar esse utilizador como membro do papel "db_owner". Aprenda a fazê-lo seguindo [esta secção](../../synapse-analytics/sql-data-warehouse/sql-data-warehouse-overview-manage-security.md#authorization).

### <a name="row-size-and-data-type-limitation"></a>Tamanho da linha e limitação do tipo de dados
As cargas de polibase limitam-se a linhas de carregamento inferiores a **1 MB** e não podem ser carregadas para VARCHR (MAX), NVARCHAR(MAX) ou VARBINARY (MAX). Consulte [aqui](../../synapse-analytics/sql-data-warehouse/sql-data-warehouse-service-capacity-limits.md#loads).

Se tiver dados de origem com linhas de tamanho superiores a 1 MB, pode querer dividir as tabelas de origem verticalmente em várias pequenas onde o maior tamanho de cada uma delas não exceda o limite. As tabelas mais pequenas podem então ser carregadas utilizando a PolyBase e fundidas em Azure SQL Data Warehouse.

### <a name="sql-data-warehouse-resource-class"></a>Classe de recursos do Armazém de Dados SQL
Para obter a melhor entrada possível, considere atribuir uma maior classe de recursos ao utilizador que está a ser utilizado para carregar dados no SQL Data Warehouse via PolyBase. Aprenda a fazê-lo seguindo alterar um exemplo de [classe de recursos do utilizador](../../sql-data-warehouse/sql-data-warehouse-develop-concurrency.md).

### <a name="tablename-in-azure-sql-data-warehouse"></a>nome de tabela no Armazém de Dados Azure SQL
A tabela seguinte fornece exemplos sobre como especificar a propriedade **tableName** no conjunto de dados JSON para várias combinações de esquema e nome de mesa.

| DB Schema | Nome da tabela | propriedade JSON nome de mesa |
| --- | --- | --- |
| dbo |MyTable |MyTable ou dbo. MyTable ou [dbo]. [MyTable] |
| dbo1 |MyTable |dbo1. MyTable ou [dbo1]. [MyTable] |
| dbo |A minha.mesa |[My.Table] ou [dbo]. [My.Table] |
| dbo1 |A minha.mesa |[dbo1]. [My.Table] |

Se vir o seguinte erro, pode ser um problema com o valor especificado para a propriedade tableName. Consulte a tabela para especificar os valores da propriedade JSON com nome de mesa.

```
Type=System.Data.SqlClient.SqlException,Message=Invalid object name 'stg.Account_test'.,Source=.Net SqlClient Data Provider
```

### <a name="columns-with-default-values"></a>Colunas com valores predefinidos
Atualmente, a funcionalidade PolyBase na Data Factory apenas aceita o mesmo número de colunas que na tabela-alvo. Digamos, tem uma tabela com quatro colunas e uma delas é definida com um valor predefinido. Os dados de entrada devem ainda conter quatro colunas. O fornecimento de um conjunto de dados de entrada de 3 colunas produziria um erro semelhante ao seguinte mensagem:

```
All columns of the table must be specified in the INSERT BULK statement.
```
O valor NULO é uma forma especial de valor predefinido. Se a coluna for anulada, os dados de entrada (em blob) para essa coluna podem estar vazios (não pode faltar do conjunto de dados de entrada). PolyBase insere NULL para eles no Armazém de Dados Azure SQL.

## <a name="auto-table-creation"></a>Criação de mesa auto
Se estiver a utilizar o Copy Wizard para copiar dados do SQL Server ou da Base de Dados Azure SQL para o Azure SQL Data Warehouse e a tabela que corresponde à tabela de origem não existe na loja de destino, a Data Factory pode criar automaticamente a tabela no armazém de dados utilizando o esquema da tabela de origem.

Data Factory cria a tabela na loja de destino com o mesmo nome de tabela na loja de dados de origem. Os tipos de dados para colunas são escolhidos com base no seguinte mapeamento do tipo. Se necessário, executa conversões de tipo para corrigir quaisquer incompatibilidades entre as lojas de origem e destino. Também usa distribuição de mesa Round Robin.

| Tipo de coluna de base de dados Source SQL | Modelo de coluna SQL DW de destino (limitação de tamanho) |
| --- | --- |
| int | int |
| BigInt | BigInt |
| SmallInt | SmallInt |
| TinyInt | TinyInt |
| Bit | Bit |
| Decimal | Decimal |
| Numérico | Decimal |
| Float | Float |
| Money | Money |
| Real | Real |
| SmallMoney | SmallMoney |
| Binário | Binário |
| Varbinary | Varbinary (até 8000) |
| Date | Date |
| DateTime | DateTime |
| Data2 | Data2 |
| Hora | Hora |
| DataTimeOffset | DataTimeOffset |
| Hora do Pequeno Encontro | Hora do Pequeno Encontro |
| Texto | Varchar (até 8000) |
| NText | NVarChar (até 4000) |
| Imagem | VarBinary (até 8000) |
| Identificador Único | Identificador Único |
| Char | Char |
| Rio Nchar | Rio Nchar |
| Rio Varchar | VarChar (até 8000) |
| Rio NVarchar | NVarChar (até 4000) |
| Xml | Varchar (até 8000) |

[!INCLUDE [data-factory-type-repeatability-for-sql-sources](../../../includes/data-factory-type-repeatability-for-sql-sources.md)]

## <a name="type-mapping-for-azure-sql-data-warehouse"></a>Mapeamento de tipo para Armazém de Dados Azure SQL
Conforme mencionado no artigo de atividades de movimento de [dados,](data-factory-data-movement-activities.md) a atividade de cópia realiza conversões automáticas de tipos de origem para tipos de sink com a seguinte abordagem de 2 passos:

1. Converter de tipos de origem nativa para .NET tipo
2. Converter do tipo .NET para o tipo de pia nativa

Ao mover dados para & do Azure SQL Data Warehouse, os seguintes mapeamentos são utilizados do tipo SQL para o tipo .NET e vice-versa.

O mapeamento é o mesmo que o Mapeamento do Tipo de Dados do [Servidor SQL para ADO.NET](https://msdn.microsoft.com/library/cc716729.aspx).

| Tipo de motor de base de dados do servidor SQL | Tipo de quadro .NET |
| --- | --- |
| bigint |Int64 |
| binary |Byte[] |
| bit |Booleano |
| char |String, Char[] |
| date |DateTime |
| Datetime |DateTime |
| datetime2 |DateTime |
| Datatimeoffset |DataTimeOffset |
| Decimal |Decimal |
| Atributo FILESTREAM (varbinary(max)) |Byte[] |
| Float |Double |
| image |Byte[] |
| int |Int32 |
| dinheiro |Decimal |
| nchar |String, Char[] |
| ntexto |String, Char[] |
| numeric |Decimal |
| nvarchar |String, Char[] |
| real |Único |
| linhaversão |Byte[] |
| tempo de data pequena |DateTime |
| smallint |Int16 |
| dinheiro pequeno |Decimal |
| sql_variant |Objeto * |
| texto |String, Char[] |
| hora |TimeSpan |
| carimbo de data/hora |Byte[] |
| tinyint |Byte |
| uniqueidentifier |GUID |
| varbinary |Byte[] |
| varchar |String, Char[] |
| xml |Xml |

Também pode mapear colunas desde o conjunto de dados de origem até colunas a partir do conjunto de dados do sumidouro na definição de atividade da cópia. Para mais detalhes, consulte [mapeiar colunas de conjuntos](data-factory-map-columns.md)de dados na Azure Data Factory .

## <a name="json-examples-for-copying-data-to-and-from-sql-data-warehouse"></a>Exemplos jSON para copiar dados de e para o Armazém de Dados SQL
Os exemplos seguintes fornecem definições JSON de amostra que pode usar para criar um pipeline utilizando [o Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) ou o [Azure PowerShell](data-factory-copy-activity-tutorial-using-powershell.md). Mostram como copiar dados de e para o Azure SQL Data Warehouse e Azure Blob Storage. No entanto, os dados podem ser copiados **diretamente** de qualquer fonte para qualquer um dos lavatórios [aqui](data-factory-data-movement-activities.md#supported-data-stores-and-formats) indicados utilizando a Atividade de Cópia na Fábrica de Dados Azure.

### <a name="example-copy-data-from-azure-sql-data-warehouse-to-azure-blob"></a>Exemplo: Copiar dados do Armazém de Dados Azure SQL para O Blob Azure
A amostra define as seguintes entidades data Factory:

1. Um serviço ligado do tipo [AzureSqlDW](#linked-service-properties).
2. Um serviço ligado do tipo [AzureStorage.](data-factory-azure-blob-connector.md#linked-service-properties)
3. Um conjunto de [dados](data-factory-create-datasets.md) de entrada do tipo [AzureSqlDWTable](#dataset-properties).
4. Um [conjunto](data-factory-create-datasets.md) de dados de saída do tipo [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties).
5. Um [pipeline](data-factory-create-pipelines.md) com Copy Activity que utiliza [SqlDWSource](#copy-activity-properties) e [BlobSink](data-factory-azure-blob-connector.md#copy-activity-properties).

A amostra copia dados da série de tempo (hora, diariamente, etc.) de uma tabela na base de dados do Armazém de Dados Azure SQL para uma bolha a cada hora. As propriedades JSON utilizadas nestas amostras são descritas em secções que seguem as amostras.

**Serviço ligado ao Armazém de Dados Azure SQL:**

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
**Serviço ligado ao armazenamento Azure Blob:**

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
**Conjunto de dados de entrada do Armazém de Dados Azure SQL:**

A amostra pressupõe que criou uma tabela "MyTable" no Azure SQL Data Warehouse e contém uma coluna chamada "timestampcolumn" para dados da série time.

Definição "externa": "verdadeira" informa o serviço data Factory de que o conjunto de dados é externo à fábrica de dados e não é produzido por uma atividade na fábrica de dados.

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
**Conjunto de dados de saída de Azure Blob:**

Os dados são escritos para uma nova bolha a cada hora (frequência: hora, intervalo: 1). O caminho da pasta para a bolha é avaliado dinamicamente com base no tempo de início da fatia que está a ser processada. O caminho da pasta utiliza partes ano, mês, dia e horas da hora de início.

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

**Copiar atividade num oleoduto com SqlDWSource e BlobSink:**

O pipeline contém uma Atividade de Cópia que está configurada para utilizar os conjuntos de dados de entrada e saída e está programado para funcionar a cada hora. Na definição JSON do gasoduto, o tipo de **origem** é definido para **SqlDWSource** e o tipo de **pia** é definido para **BlobSink**. A consulta SQL especificada para a propriedade **SqlReaderQuery** seleciona os dados na última hora para copiar.

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
> No exemplo, o **sqlReaderQuery** é especificado para o SqlDWSource. A Atividade de Cópia executa esta consulta contra a fonte do Armazém de Dados Azure SQL para obter os dados.
>
> Em alternativa, pode especificar um procedimento armazenado especificando o nome de **procedimento sqlReaderStoredEdE** e **os parâmetros de procedimento armazenados** (se o procedimento armazenado tiver parâmetros).
>
> Se não especificar o sqlReaderQuery ou o sqlReaderStoredProcedureName, as colunas definidas na secção de estrutura do conjunto de dados JSON são usadas para construir uma consulta (selecione coluna1, coluna2 a partir de míitem) para ser executada contra o Armazém de Dados Azure SQL. Se a definição de conjunto de dados não tiver a estrutura, todas as colunas são selecionadas a partir da tabela.
>
>

### <a name="example-copy-data-from-azure-blob-to-azure-sql-data-warehouse"></a>Exemplo: Copiar dados de Azure Blob para Armazém de Dados Azure SQL
A amostra define as seguintes entidades data Factory:

1. Um serviço ligado do tipo [AzureSqlDW](#linked-service-properties).
2. Um serviço ligado do tipo [AzureStorage.](data-factory-azure-blob-connector.md#linked-service-properties)
3. Um conjunto de [dados](data-factory-create-datasets.md) de entrada do tipo [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties).
4. Um [conjunto](data-factory-create-datasets.md) de dados de saída do tipo [AzureSqlDWTable](#dataset-properties).
5. Um [pipeline](data-factory-create-pipelines.md) com atividade copy que utiliza [BlobSource](data-factory-azure-blob-connector.md#copy-activity-properties) e [SqlDWSink](#copy-activity-properties).

A amostra copia dados da série tempora (hora, diariamente, etc.) da blob Azure para uma tabela na base de dados do Armazém de Dados Azure SQL a cada hora. As propriedades JSON utilizadas nestas amostras são descritas em secções que seguem as amostras.

**Serviço ligado ao Armazém de Dados Azure SQL:**

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
**Serviço ligado ao armazenamento Azure Blob:**

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
**Conjunto de dados de entrada Azure Blob:**

Os dados são recolhidos a partir de uma nova bolha a cada hora (frequência: hora, intervalo: 1). O caminho da pasta e o nome do ficheiro para a bolha são avaliados dinamicamente com base no tempo de início da fatia que está a ser processada. O caminho da pasta utiliza ano, mês e parte do dia da hora de início e o nome do ficheiro utiliza a parte da hora da hora de início. "externa": a definição "verdadeira" informa o serviço data Factory de que esta tabela é externa à fábrica de dados e não é produzida por uma atividade na fábrica de dados.

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
**Conjunto de dados de saída do Armazém de Dados Azure SQL:**

A amostra copia dados para uma tabela chamada "MyTable" no Azure SQL Data Warehouse. Crie a tabela no Azure SQL Data Warehouse com o mesmo número de colunas que espera que o ficheiro Blob CSV contenha. Novas filas são adicionadas à mesa a cada hora.

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
**Copiar atividade num oleoduto com BlobSource e SqlDWSink:**

O pipeline contém uma Atividade de Cópia que está configurada para utilizar os conjuntos de dados de entrada e saída e está programado para funcionar a cada hora. Na definição JSON do gasoduto, o tipo de **origem** é definido para **BlobSource** e o tipo **de pia** é definido para **SqlDWSink**.

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
Para um passeio, consulte a ver [Load 1 TB no Azure SQL Data Warehouse em menos de 15 minutos com](data-factory-load-sql-data-warehouse.md) a Azure Data Factory e carregue os dados com o artigo da [Azure Data Factory](../../sql-data-warehouse/sql-data-warehouse-get-started-load-with-azure-data-factory.md) na documentação do Armazém de Dados Azure SQL.

## <a name="performance-and-tuning"></a>Desempenho e Afinação
Consulte o [Copy Activity Performance & Tuning Guide](data-factory-copy-activity-performance.md) para conhecer os fatores-chave que impactam o desempenho do movimento de dados (Copy Activity) na Fábrica de Dados Do Azure e várias formas de o otimizar.
