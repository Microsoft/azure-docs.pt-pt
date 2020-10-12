---
title: Copiar dados de/para a Azure Synapse Analytics (anteriormente SQL Data Warehouse)
description: Saiba como copiar dados de/para a Azure Synapse Analytics (anteriormente SQL Data Warehouse) utilizando a Azure Data Factory
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
ms.openlocfilehash: b7324115c880fb1ee4d5a1730a3b84a289cee4b0
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "89490144"
---
# <a name="copy-data-to-and-from-azure-synapse-analytics-formerly-sql-data-warehouse-using-azure-data-factory"></a>Copiar dados de e para a Azure Synapse Analytics (anteriormente SQL Data Warehouse) usando Azure Data Factory
> [!div class="op_single_selector" title1="Selecione a versão do serviço Data Factory que está a utilizar:"]
> * [Versão 1](data-factory-azure-sql-data-warehouse-connector.md)
> * [Versão 2 (versão atual)](../connector-azure-sql-data-warehouse.md)

> [!NOTE]
> Este artigo aplica-se à versão 1 do Data Factory. Se estiver a utilizar a versão atual do serviço Data Factory, consulte o [conector Azure Synapse Analytics em V2](../connector-azure-sql-data-warehouse.md).

Este artigo explica como utilizar a Atividade de Cópia na Fábrica de Dados Azure para mover dados de/para a Azure Synapse Analytics. Baseia-se no artigo de Atividades de Movimento de [Dados,](data-factory-data-movement-activities.md) que apresenta uma visão geral do movimento de dados com a atividade da cópia.

> [!TIP]
> Para obter o melhor desempenho, utilize a PolyBase para carregar dados no Azure Synapse Analytics. A [Utilização polyBase para carregar dados na secção Azure Synapse Analytics](#use-polybase-to-load-data-into-azure-synapse-analytics) tem detalhes. Para uma passagem com uma caixa de utilização, consulte [a Carga 1 TB em Azure Synapse Analytics em menos de 15 minutos com a Azure Data Factory](data-factory-load-sql-data-warehouse.md).

## <a name="supported-scenarios"></a>Cenários suportados
Pode copiar dados **da Azure Synapse Analytics** para as seguintes lojas de dados:

[!INCLUDE [data-factory-supported-sinks](../../../includes/data-factory-supported-sinks.md)]

Pode copiar dados das seguintes lojas de dados **para a Azure Synapse Analytics:**

[!INCLUDE [data-factory-supported-sources](../../../includes/data-factory-supported-sources.md)]

> [!TIP]
> Ao copiar dados do SQL Server ou da Base de Dados Azure SQL para Azure Synapse Analytics, se a tabela não existir na loja de destino, a Data Factory pode criar automaticamente a tabela no Synapse Analytics utilizando o esquema da tabela na loja de dados de origem. Consulte [a criação da tabela Auto](#auto-table-creation) para mais detalhes.

## <a name="supported-authentication-type"></a>Tipo de autenticação suportada
O conector Azure Synapse Analytics suporta a autenticação básica.

## <a name="getting-started"></a>Introdução
Pode criar um pipeline com uma atividade de cópia que move dados de/para um Azure Synapse Analytics utilizando diferentes ferramentas/APIs.

A forma mais fácil de criar um pipeline que copie dados de/para a Azure Synapse Analytics é utilizar o assistente de dados Copy. Consulte [Tutorial: Carregue os dados em Synapse Analytics com Data Factory](../../sql-data-warehouse/sql-data-warehouse-load-with-data-factory.md) para uma rápida passagem pela criação de um pipeline utilizando o assistente de dados Copy.

Também pode utilizar as seguintes ferramentas para criar um pipeline: **Visual Studio**, **Azure PowerShell,** **Azure Resource Manager,** **.NET API**e **REST API**. Consulte o tutorial de [atividade de cópia](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) para obter instruções passo a passo para criar um oleoduto com uma atividade de cópia.

Quer utilize as ferramentas ou APIs, executa os seguintes passos para criar um pipeline que transfere dados de uma loja de dados de origem para uma loja de dados de lavatórios:

1. Criar uma fábrica de **dados.** Uma fábrica de dados pode conter um ou mais oleodutos. 
2. Crie **serviços ligados** para ligar as lojas de dados de entrada e saída à sua fábrica de dados. Por exemplo, se estiver a copiar dados de um armazenamento de blob Azure para um Azure Synapse Analytics, cria dois serviços ligados para ligar a sua conta de armazenamento Azure e a Azure Synapse Analytics à sua fábrica de dados. Para propriedades de serviço ligadas específicas da Azure Synapse Analytics, consulte a secção [de propriedades de serviços ligadas.](#linked-service-properties) 
3. Crie **conjuntos de dados** para representar dados de entrada e saída para a operação de cópia. No exemplo mencionado no último passo, cria-se um conjunto de dados para especificar o recipiente blob e a pasta que contém os dados de entrada. E cria outro conjunto de dados para especificar a tabela no Azure Synapse Analytics que detém os dados copiados do armazenamento do blob. Para propriedades de conjunto de dados específicas do Azure Synapse Analytics, consulte a secção [de propriedades do conjunto de dados.](#dataset-properties)
4. Crie um **pipeline** com uma atividade de cópia que leva um conjunto de dados como entrada e um conjunto de dados como uma saída. No exemplo mencionado anteriormente, utiliza-se o BlobSource como fonte e o SqlDWSink como pia para a atividade da cópia. Da mesma forma, se estiver a copiar de Azure Synapse Analytics para Azure Blob Storage, utilize SqlDWSource e BlobSink na atividade da cópia. Para propriedades de atividade de cópia específicas do Azure Synapse Analytics, consulte a secção [de propriedades da atividade da cópia.](#copy-activity-properties) Para obter mais informações sobre como utilizar uma loja de dados como fonte ou pia, clique no link na secção anterior para a sua loja de dados.

Quando utiliza o assistente, as definições de JSON para estas entidades da Data Factory (serviços ligados, conjuntos de dados e o pipeline) são automaticamente criadas para si. Quando utiliza ferramentas/APIs (exceto .NET API), define estas entidades da Data Factory utilizando o formato JSON. Para amostras com definições JSON para entidades da Data Factory que são usadas para copiar dados de/para Azure Synapse Analytics, consulte a secção de [exemplos JSON](#json-examples-for-copying-data-to-and-from-azure-synapse-analytics) deste artigo.

As seguintes secções fornecem detalhes sobre as propriedades da JSON que são usadas para definir entidades da Data Factory específicas da Azure Synapse Analytics:

## <a name="linked-service-properties"></a>Propriedades de serviço ligadas
A tabela seguinte fornece descrição para elementos JSON específicos do serviço Azure Synapse Analytics ligado.

| Propriedade | Descrição | Obrigatório |
| --- | --- | --- |
| tipo |A propriedade tipo deve ser definida para: **AzureSqlDW** |Sim |
| conexãoStragem |Especifique as informações necessárias para ligar à instância Azure Synapse Analytics para a propriedade connectionString. Apenas a autenticação básica é suportada. |Sim |

> [!IMPORTANT]
> Configure [Azure SQL Database Firewall](https://msdn.microsoft.com/library/azure/ee621782.aspx#ConnectingFromAzure) e o servidor de base de dados para permitir que os [Serviços Azure acedam ao servidor](https://msdn.microsoft.com/library/azure/ee621782.aspx#ConnectingFromAzure). Além disso, se estiver a copiar dados para a Azure Synapse Analytics de fora do Azure, incluindo de fontes de dados no local com porta de entrada de fábrica de dados, configufique a gama de endereços IP apropriada para a máquina que está a enviar dados para a Azure Synapse Analytics.

## <a name="dataset-properties"></a>Dataset properties (Propriedades do conjunto de dados)
Para obter uma lista completa de secções & propriedades disponíveis para definir conjuntos de dados, consulte o artigo [Criar conjuntos de dados.](data-factory-create-datasets.md) Secções como estrutura, disponibilidade e política de um conjunto de dados JSON são semelhantes para todos os tipos de conjunto de dados (Azure SQL, Azure blob, Azure table, etc.).

A secção typeProperties é diferente para cada tipo de conjunto de dados e fornece informações sobre a localização dos dados na loja de dados. A secção **de tipos de direitos** para o conjunto de dados do tipo **AzureSqlDWTable** tem as seguintes propriedades:

| Propriedade | Descrição | Obrigatório |
| --- | --- | --- |
| tableName |Nome da tabela ou vista na base de dados Azure Synapse Analytics a que o serviço ligado se refere. |Sim |

## <a name="copy-activity-properties"></a>Propriedades da atividade Copy
Para obter uma lista completa das secções & propriedades disponíveis para definir atividades, consulte o artigo [Criar Pipelines.](data-factory-create-pipelines.md) Propriedades como nome, descrição, tabelas de entrada e saída, e política estão disponíveis para todos os tipos de atividades.

> [!NOTE]
> A Atividade de Cópia leva apenas uma entrada e produz apenas uma saída.

Enquanto que as propriedades disponíveis na secção de tipos de atividade variam com cada tipo de atividade. Para a atividade copy, variam dependendo dos tipos de fontes e pias.

### <a name="sqldwsource"></a>SqlDWSource
Quando a fonte é do tipo **SqlDWSource,** as seguintes propriedades estão disponíveis na secção **typeProperties:**

| Propriedade | Descrição | Valores permitidos | Necessário |
| --- | --- | --- | --- |
| sqlReaderQuery |Utilize a consulta personalizada para ler dados. |Cadeia de consulta SQL. Por exemplo: selecione * do MyTable. |Não |
| sqlReaderStoredProcedureName |Nome do procedimento armazenado que lê dados da tabela de origem. |Nome do procedimento armazenado. A última declaração SQL deve ser uma declaração SELECT no procedimento armazenado. |Não |
| parametrómetros de reserva armazenados |Parâmetros para o procedimento armazenado. |Pares de nomes/valores. Os nomes e o invólucro dos parâmetros devem corresponder aos nomes e invólucros dos parâmetros de procedimento armazenados. |Não |

Se o **SqlReaderQuery** for especificado para o SqlDWSource, a Atividade de Cópia executa esta consulta com a fonte Azure Synapse Analytics para obter os dados.

Em alternativa, pode especificar um procedimento armazenado especificando o **nome sqlReaderStoredProcedureName** e **os Computadores Deprocedures armazenados** (se o procedimento armazenado tiver parâmetros).

Se não especificar nem sqlReaderQuery ou sqlReaderStorEdProcedureName, as colunas definidas na secção de estrutura do conjunto de dados JSON são usadas para construir uma consulta para correr contra a Azure Synapse Analytics. Exemplo: `select column1, column2 from mytable`. Se a definição de conjunto de dados não tiver a estrutura, todas as colunas são selecionadas a partir da tabela.

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
**Definição de procedimento armazenado:**

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
| sqlWriterCleanUpScript |Especifique uma consulta para a Copy Activity para executar de modo a que os dados de uma fatia específica seja limpo. Para mais detalhes, consulte a [secção de repetibilidade](#repeatability-during-copy). |Uma declaração de consulta. |Não |
| permitir A Base DePoly |Indica se deve utilizar a PolyBase (quando aplicável) em vez do mecanismo BULKINSERT. <br/><br/> **A utilização do PolyBase é a forma recomendada de carregar dados no Azure Synapse Analytics.** Consulte [o PolyBase para carregar dados na secção Azure Synapse Analytics](#use-polybase-to-load-data-into-azure-synapse-analytics) para obter constrangimentos e detalhes. |Verdadeiro <br/>Falso (predefinição) |Não |
| poliBaseSettings |Um grupo de propriedades que podem ser especificadas quando a propriedade **allowPolybase** é definida como **verdadeira**. |&nbsp; |Não |
| rejeitarValue |Especifica o número ou percentagem de linhas que podem ser rejeitadas antes da consulta falhar. <br/><br/>Saiba mais sobre as opções de rejeição da PolyBase na secção **de Argumentos** do tema CREATE EXTERNAL [TABLE (Transact-SQL).](https://msdn.microsoft.com/library/dn935021.aspx) |0 (padrão), 1, 2, ... |Não |
| rejeitarType |Especifica se a opção rejeitar oValue é especificada como um valor literal ou uma percentagem. |Valor (padrão), Percentagem |Não |
| rejeitarSampleValue |Determina o número de linhas a recuperar antes que o PolyBase recalcule a percentagem de linhas rejeitadas. |1, 2, ... |Sim, se **rejeitarType** é **percentagem** |
| utilizarTypeDefault |Especifica como lidar com valores em falta em ficheiros de texto delimitados quando o PolyBase recupera dados do ficheiro de texto.<br/><br/>Saiba mais sobre esta propriedade a partir da secção Argumentos em [FORMATO DE FICHEIRO EXTERNO (Transact-SQL)](https://msdn.microsoft.com/library/dn935026.aspx). |Verdadeiro, Falso (padrão) |Não |
| escreverBatchSize |Insere dados na tabela SQL quando o tamanho do tampão atinge o writeBatchSize |Inteiro (número de linhas) |Não (padrão: 10000) |
| escreverBatchTimeout |Tempo de espera para que o funcionamento do encaixe do lote esteja concluído antes de esgotar o tempo. |timespan<br/><br/> Exemplo: "00:30:00" (30 minutos). |Não |

#### <a name="sqldwsink-example"></a>Exemplo sqlDWSink

```JSON
"sink": {
    "type": "SqlDWSink",
    "allowPolyBase": true
}
```

## <a name="use-polybase-to-load-data-into-azure-synapse-analytics"></a>Utilize a PolyBase para carregar dados em Azure Synapse Analytics
A utilização do **[PolyBase](https://docs.microsoft.com/sql/relational-databases/polybase/polybase-guide)** é uma forma eficiente de carregar uma grande quantidade de dados no Azure Synapse Analytics com alta produção. Pode ver um grande ganho na produção utilizando o PolyBase em vez do mecanismo padrão BULKINSERT. Consulte [o número de referência de desempenho da cópia](data-factory-copy-activity-performance.md#performance-reference) com comparação detalhada. Para uma passagem com uma caixa de utilização, consulte [a Carga 1 TB em Azure Synapse Analytics em menos de 15 minutos com a Azure Data Factory](data-factory-load-sql-data-warehouse.md).

* Se os seus dados de origem estiverem na **Azure Blob ou na Azure Data Lake Store,** e o formato for compatível com a PolyBase, pode copiar diretamente para a Azure Synapse Analytics utilizando o PolyBase. Consulte **[a cópia direta utilizando o PolyBase](#direct-copy-using-polybase)** com detalhes.
* Se a sua loja de dados de origem e o seu formato não forem originalmente suportados pela PolyBase, pode utilizar a Cópia Encenada utilizando a funcionalidade **[PolyBase.](#staged-copy-using-polybase)** Também lhe proporciona uma melhor produção, convertendo automaticamente os dados em formato compatível com a PolyBase e armazenando os dados no armazenamento do Azure Blob. Em seguida, carrega dados em Azure Synapse Analytics.

Desconfiem da `allowPolyBase` propriedade como mostrado no exemplo seguinte para a Azure Data Factory utilizar o PolyBase para copiar dados no Azure Synapse Analytics. **true** Quando configurar permite que a Base Desatada seja verdadeira, pode especificar propriedades específicas da PolyBase utilizando o `polyBaseSettings` grupo de propriedade. consulte a secção [SqlDWSink](#sqldwsink) para obter detalhes sobre propriedades que pode utilizar com poliBaseSettings.

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

### <a name="direct-copy-using-polybase"></a>Cópia direta usando PolyBase
A Azure Synapse Analytics PolyBase suporta diretamente a Azure Blob e a Azure Data Lake Store (usando o principal serviço) como fonte e com requisitos específicos de formato de ficheiro. Se os seus dados de origem satisfaçam os critérios descritos nesta secção, pode copiar diretamente da loja de dados de origem para a Azure Synapse Analytics utilizando o PolyBase. Caso contrário, pode utilizar [a Cópia Encenada utilizando a PolyBase](#staged-copy-using-polybase).

> [!TIP]
> Para copiar dados da Data Lake Store para a Azure Synapse Analytics de forma eficiente, saiba mais com [a Azure Data Factory, tornando ainda mais fácil e conveniente descobrir insights a partir de dados ao utilizar a Data Lake Store com a Azure Synapse Analytics.](https://blogs.msdn.microsoft.com/azuredatalake/2017/04/08/azure-data-factory-makes-it-even-easier-and-convenient-to-uncover-insights-from-data-when-using-data-lake-store-with-sql-data-warehouse/)

Se os requisitos não forem cumpridos, a Azure Data Factory verifica as definições e volta automaticamente ao mecanismo BULKINSERT para o movimento de dados.

1. **O serviço ligado à fonte** é do tipo: **AzureStorage** ou **AzureDataLakeStore com autenticação principal do serviço**.
2. O conjunto de **dados** de entrada é do tipo: **AzureBlob** ou **AzureDataLakeStore,** e o tipo de formato sob `type` propriedades é **OrcFormat,** **ParquetFormat**ou **TextFormat** com as seguintes configurações:

   1. `rowDelimiter` deve ser **\n**.
   2. `nullValue` está definido para **corda vazia** (""), ou está definido `treatEmptyAsNull` para **verdade**.
   3. `encodingName`está definido para **utf-8**, que é o valor **padrão.**
   4. `escapeChar`, `quoteChar` `firstRowAsHeader` e `skipLineCount` não estão especificados.
   5. `compression` não pode ser **compressões,** **GZip,** ou **Deflate**.

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

3. Não existe `skipHeaderLineCount` nenhuma definição no **BlobSource** ou **na AzureDataLakeStore** para a atividade copy no pipeline.
4. Não existe `sliceIdentifierColumnName` uma definição no **SqlDWSink** para a atividade copy no oleoduto. (A PolyBase garante que todos os dados são atualizados ou nada é atualizado numa única execução. Para obter **a repetibilidade,** pode `sqlWriterCleanupScript` utilizar).
5. Não há `columnMapping` utilização na atividade associada na copy.

### <a name="staged-copy-using-polybase"></a>Cópia encenada usando PolyBase
Quando os dados de origem não satisfaçam os critérios introduzidos na secção anterior, pode ativar a cópia dos dados através de uma encenação provisória Azure Blob Storage (não pode ser Armazenamento Premium). Neste caso, a Azure Data Factory realiza automaticamente transformações nos dados para satisfazer os requisitos do formato de dados da PolyBase, depois utiliza o PolyBase para carregar dados no Azure Synapse Analytics e, finalmente, limpa os dados temporários do armazenamento blob. Consulte [a Cópia Encenada](data-factory-copy-activity-performance.md#staged-copy) para obter mais detalhes sobre como copiar dados através de uma encenação Azure Blob funciona em geral.

> [!NOTE]
> Ao copiar dados de uma loja de dados nas instalações para a Azure Synapse Analytics utilizando a PolyBase e a encenação, se a sua versão Data Management Gateway estiver abaixo de 2.4, o JRE (Java Runtime Environment) é necessário na sua máquina de gateway que é usada para transformar os seus dados de origem em formato adequado. Sugiro que atualize a sua porta de entrada para o mais tardar para evitar tal dependência.
>

Para utilizar esta função, crie um [serviço ligado ao Azure Storage](data-factory-azure-blob-connector.md#azure-storage-linked-service) que se refira à Conta de Armazenamento Azure que tem o armazenamento provisório do blob, especificando então as propriedades e propriedades para a Atividade de `enableStaging` `stagingSettings` Cópia, conforme mostrado no seguinte código:

```json
"activities":[
{
    "name": "Sample copy activity from SQL Server to Azure Synapse Analytics via PolyBase",
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

## <a name="best-practices-when-using-polybase"></a>Melhores práticas ao utilizar o PolyBase
As seguintes secções fornecem boas práticas adicionais às que são mencionadas nas [melhores práticas para o Azure Synapse Analytics](../../synapse-analytics/sql-data-warehouse/sql-data-warehouse-best-practices.md).

### <a name="required-database-permission"></a>Autorização de base de dados necessária
Para utilizar o PolyBase, requer que o utilizador seja utilizado para carregar dados no Azure Synapse Analytics tem a [permissão "CONTROL"](https://msdn.microsoft.com/library/ms191291.aspx) na base de dados-alvo. Uma forma de o conseguir é adicionar esse utilizador como membro do papel "db_owner". Aprenda a fazê-lo seguindo [esta secção.](../../synapse-analytics/sql-data-warehouse/sql-data-warehouse-overview-manage-security.md#authorization)

### <a name="row-size-and-data-type-limitation"></a>Tamanho da linha e limitação do tipo de dados
As cargas de base poli-base limitam-se a cargas inferiores a **1 MB** e não podem ser carregadas para VARCHR(MAX), NVARCHAR(MAX) ou VARBINARY (MAX). Consulte [aqui.](../../synapse-analytics/sql-data-warehouse/sql-data-warehouse-service-capacity-limits.md#loads)

Se tiver dados de origem com linhas de tamanho superior a 1 MB, pode querer dividir as tabelas de origem verticalmente em várias pequenas onde o maior tamanho de linha de cada um deles não excede o limite. As tabelas mais pequenas podem então ser carregadas usando a PolyBase e fundidas em Azure Synapse Analytics.

### <a name="azure-synapse-analytics-resource-class"></a>Classe de recursos Azure Synapse Analytics
Para obter o melhor rendimento possível, considere atribuir uma classe de recursos maior ao utilizador que está a ser utilizado para carregar dados em Azure Synapse Analytics via PolyBase. Aprenda a fazê-lo seguindo [O exemplo da classe de recursos do utilizador.](../../sql-data-warehouse/sql-data-warehouse-develop-concurrency.md)

### <a name="tablename-in-azure-synapse-analytics"></a>nome de mesa em Azure Synapse Analytics
A tabela seguinte fornece exemplos sobre como especificar a propriedade **tableName** no conjunto de dados JSON para várias combinações de esquema e nome de mesa.

| DB Schema | Nome da tabela | tableName JSON propriedade |
| --- | --- | --- |
| dbo |MyTable |MyTable ou dbo. MyTable ou [dbo]. [MyTable] |
| dbo1 |MyTable |dbo1. MyTable ou [dbo1]. [MyTable] |
| dbo |A minha.mesa |[My.Table] ou [dbo]. [Minha.Mesa] |
| dbo1 |A minha.mesa |[dbo1]. [Minha.Mesa] |

Se vir o seguinte erro, pode ser um problema com o valor especificado para a propriedade tableName. Consulte a tabela para obter a forma correta de especificar valores para a propriedade JSON de nome de mesa.

```
Type=System.Data.SqlClient.SqlException,Message=Invalid object name 'stg.Account_test'.,Source=.Net SqlClient Data Provider
```

### <a name="columns-with-default-values"></a>Colunas com valores predefinidos
Atualmente, a funcionalidade PolyBase na Data Factory apenas aceita o mesmo número de colunas que na tabela-alvo. Tem uma tabela com quatro colunas e uma delas é definida com um valor padrão. Os dados de entrada devem ainda conter quatro colunas. Fornecer um conjunto de dados de entrada de 3 colunas produziria um erro semelhante à seguinte mensagem:

```
All columns of the table must be specified in the INSERT BULK statement.
```
O valor NULO é uma forma especial de valor padrão. Se a coluna for anulada, os dados de entrada (em bolha) para essa coluna podem estar vazios (não podem faltar do conjunto de dados de entrada). PolyBase insere NU NUN para eles no Azure Synapse Analytics.

## <a name="auto-table-creation"></a>Criação de mesa de automóveis
Se estiver a utilizar o Copy Wizard para copiar dados do SQL Server ou da Base de Dados Azure SQL para a Azure Synapse Analytics e a tabela que corresponde à tabela de origem não existe na loja de destino, a Data Factory pode criar automaticamente a tabela no armazém de dados utilizando o esquema da tabela de origem.

A Data Factory cria a tabela na loja de destino com o mesmo nome de tabela na loja de dados de origem. Os tipos de dados para colunas são escolhidos com base no seguinte mapeamento do tipo. Se necessário, executa conversões de tipo para corrigir eventuais incompatibilidades entre lojas de origem e destino. Também usa a distribuição da mesa Round Robin.

| Tipo de coluna de base de dados SQL de origem | Destino Azure Synapse Analytics tipo de coluna (limitação de tamanho) |
| --- | --- |
| int | int |
| BigInt | BigInt |
| PequenoInt | PequenoInt |
| TinyInt | TinyInt |
| Pouco | Pouco |
| Decimal | Decimal |
| Numérico | Decimal |
| Float | Float |
| Money | Money |
| Real | Real |
| SmallMoney | SmallMoney |
| Binário | Binário |
| Varbinário | Varbinário (até 8000) |
| Date | Date |
| DateTime | DateTime |
| DataTime2 | DataTime2 |
| Hora | Hora |
| Início de execução de tempo de data | Início de execução de tempo de data |
| Hora do Pequeno Natal | Hora do Pequeno Natal |
| Texto | Varchar (até 8000) |
| NText | NVarChar (até 4000) |
| Imagem | VarBinário (até 8000) |
| Identificador Único | Identificador Único |
| Char | Char |
| NChar | NChar |
| Rio VarChar | VarChar (até 8000) |
| NVarChar | NVarChar (até 4000) |
| Xml | Varchar (até 8000) |

[!INCLUDE [data-factory-type-repeatability-for-sql-sources](../../../includes/data-factory-type-repeatability-for-sql-sources.md)]

## <a name="type-mapping-for-azure-synapse-analytics"></a>Mapeamento de tipo para Azure Synapse Analytics
Conforme mencionado no artigo [de atividades](data-factory-data-movement-activities.md) de movimento de dados, a atividade copy realiza conversões automáticas de tipo de origem para tipos de pia com a seguinte abordagem de 2 etapas:

1. Converter de tipos de origem nativa para .NET tipo
2. Converter de tipo .NET para tipo de pia nativa

Ao mover dados para & da Azure Synapse Analytics, os seguintes mapeamentos são usados do tipo SQL para .NET tipo e vice-versa.

O mapeamento é o mesmo que o Mapeamento do [Tipo de Dados do Servidor SQL para ADO.NET](https://msdn.microsoft.com/library/cc716729.aspx).

| Tipo de motor de base de dados de servidor SQL | .NET Tipo de quadro |
| --- | --- |
| bigint |Int64 |
| binary |Byte[] |
| bit |Booleano |
| char |String, Char[] |
| date |DateTime |
| Datetime |DateTime |
| datetime2 |DateTime |
| Datatimeoff |Início de execução de tempo de data |
| Decimal |Decimal |
| Atributo FILESTREAM (varbinário(máx)) |Byte[] |
| Float |Double (Duplo) |
| image |Byte[] |
| int |Int32 |
| dinheiro |Decimal |
| nchar |String, Char[] |
| ntext |String, Char[] |
| numeric |Decimal |
| nvarchar |String, Char[] |
| real |Único |
| rowversão |Byte[] |
| hora pequena |DateTime |
| smallint |Int16 |
| pequeno dinheiro |Decimal |
| sql_variant |Objeto * |
| texto |String, Char[] |
| hora |TimeSpan |
| carimbo de data/hora |Byte[] |
| tinyint |Byte |
| uniqueidentifier |GUID |
| varbinário |Byte[] |
| varchar |String, Char[] |
| xml |Xml |

Também pode mapear colunas de conjunto de dados de origem para colunas a partir do conjunto de dados da pia na definição de atividade de cópia. Para mais informações, consulte [as colunas mapping dataset na Azure Data Factory](data-factory-map-columns.md).

## <a name="json-examples-for-copying-data-to-and-from-azure-synapse-analytics"></a>Exemplos de JSON para copiar dados de e para a Azure Synapse Analytics
Os exemplos a seguir fornecem definições JSON de amostra que pode usar para criar um oleoduto utilizando [o Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) ou o [Azure PowerShell](data-factory-copy-activity-tutorial-using-powershell.md). Eles mostram como copiar dados de e para Azure Synapse Analytics e Azure Blob Storage. No entanto, os dados podem ser copiados **diretamente** de qualquer uma das fontes para qualquer um dos lavatórios [aqui](data-factory-data-movement-activities.md#supported-data-stores-and-formats) indicados usando a Atividade de Cópia na Fábrica de Dados Azure.

### <a name="example-copy-data-from-azure-synapse-analytics-to-azure-blob"></a>Exemplo: Copiar dados da Azure Synapse Analytics para Azure Blob
A amostra define as seguintes entidades da Data Factory:

1. Um serviço ligado do tipo [AzureSqlDW](#linked-service-properties).
2. Um serviço ligado do tipo [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties).
3. Um conjunto de [dados](data-factory-create-datasets.md) de entrada do tipo [AzureSqlDWTable](#dataset-properties).
4. Um conjunto de [dados](data-factory-create-datasets.md) de saída do tipo [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties).
5. Um [pipeline](data-factory-create-pipelines.md) com Copy Activity que utiliza [SqlDWSource](#copy-activity-properties) e [BlobSink](data-factory-azure-blob-connector.md#copy-activity-properties).

A amostra copia os dados de séries horárias (hora a hora, diariamente, etc.) de uma tabela na base de dados Azure Synapse Analytics para uma bolha a cada hora. As propriedades JSON utilizadas nestas amostras são descritas em secções que seguem as amostras.

**Serviço Azure Synapse Analytics ligado:**

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
**Conjunto de dados de entrada Azure Synapse Analytics:**

A amostra pressupõe que criou uma tabela "MyTable" em Azure Synapse Analytics e contém uma coluna chamada "timetampcolumn" para dados da série de tempo.

Definição "externa": "verdadeiro" informa o serviço Data Factory de que o conjunto de dados é externo à fábrica de dados e não é produzido por uma atividade na fábrica de dados.

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
**Conjunto de dados de saída Azure Blob:**

Os dados são escritos para uma nova bolha a cada hora (frequência: hora, intervalo: 1). O caminho da pasta para a bolha é avaliado dinamicamente com base na hora de início da fatia que está a ser processada. O caminho da pasta utiliza partes do ano, mês, dia e horas da hora de início.

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

O pipeline contém uma Atividade de Cópia que está configurada para utilizar os conjuntos de dados de entrada e saída e está programado para ser executado a cada hora. Na definição JSON do gasoduto, o tipo **de fonte** é definido para **SqlDWSource** e o tipo **de pia** é definido para **BlobSink**. A consulta SQL especificada para a propriedade **SqlReaderQuery** seleciona os dados na hora passada para copiar.

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
> No exemplo, **sqlReaderQuery** é especificado para o SqlDWSource. A Copy Activity executa esta consulta contra a fonte Azure Synapse Analytics para obter os dados.
>
> Em alternativa, pode especificar um procedimento armazenado especificando o **nome sqlReaderStoredProcedureName** e **os Computadores Deprocedures armazenados** (se o procedimento armazenado tiver parâmetros).
>
> Se não especificar nem sqlReaderQuery ou sqlReaderStorEdProcedureName, as colunas definidas na secção de estrutura do conjunto de dados JSON são usadas para construir uma consulta (coluna 1, coluna2 da metable) para correr contra a Azure Synapse Analytics. Se a definição de conjunto de dados não tiver a estrutura, todas as colunas são selecionadas a partir da tabela.
>
>

### <a name="example-copy-data-from-azure-blob-to-azure-synapse-analytics"></a>Exemplo: Copiar dados de Azure Blob para Azure Synapse Analytics
A amostra define as seguintes entidades da Data Factory:

1. Um serviço ligado do tipo [AzureSqlDW](#linked-service-properties).
2. Um serviço ligado do tipo [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties).
3. Um conjunto de [dados](data-factory-create-datasets.md) de entrada do tipo [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties).
4. Um conjunto de [dados](data-factory-create-datasets.md) de saída do tipo [AzureSqlDWTable](#dataset-properties).
5. Um [pipeline](data-factory-create-pipelines.md) com atividade copy que usa [BlobSource](data-factory-azure-blob-connector.md#copy-activity-properties) e [SqlDWSink](#copy-activity-properties).

A amostra copia os dados da série de tempo (hora a hora, diariamente, etc.) da bolha de Azure para uma tabela numa base de dados Azure Synapse Analytics a cada hora. As propriedades JSON utilizadas nestas amostras são descritas em secções que seguem as amostras.

**Serviço Azure Synapse Analytics ligado:**

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

Os dados são recolhidos a partir de uma nova bolha a cada hora (frequência: hora, intervalo: 1). O caminho da pasta e o nome do ficheiro para a bolha são avaliados dinamicamente com base na hora de início da fatia que está a ser processada. O percurso da pasta utiliza a parte do ano, mês e dia da hora de início e o nome do ficheiro utiliza a parte da hora da hora de início. Definição "externa": a definição "verdadeira" informa o serviço data Factory de que esta tabela é externa à fábrica de dados e não é produzida por uma atividade na fábrica de dados.

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
**Conjunto de dados de saída Azure Synapse Analytics:**

A amostra copia dados para uma tabela chamada "MyTable" em Azure Synapse Analytics. Crie a tabela em Azure Synapse Analytics com o mesmo número de colunas que espera que o ficheiro Blob CSV contenha. Novas filas são adicionadas à mesa a cada hora.

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
**Copiar a atividade num oleoduto com BlobSource e SqlDWSink:**

O pipeline contém uma Atividade de Cópia que está configurada para utilizar os conjuntos de dados de entrada e saída e está programado para ser executado a cada hora. Na definição JSON do gasoduto, o tipo **de fonte** é definido para **BlobSource** e o tipo **de pia** é definido para **SqlDWSink**.

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
Para uma passagem, consulte a [carga 1 TB em Azure Synapse Analytics em menos de 15 minutos com Azure Data Factory](data-factory-load-sql-data-warehouse.md) e Load data com o artigo [Azure Data Factory](../../sql-data-warehouse/sql-data-warehouse-get-started-load-with-azure-data-factory.md) na documentação Azure Synapse Analytics.

## <a name="performance-and-tuning"></a>Performance e Afinação
Consulte [copy Activity Performance & Guia de Afinação](data-factory-copy-activity-performance.md) para conhecer os fatores-chave que impactam o desempenho do movimento de dados (Copy Activity) na Azure Data Factory e várias formas de otimizá-lo.
