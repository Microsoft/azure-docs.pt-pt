---
title: Transformar dados utilizando a atividade do Procedimento Armazenado
description: Explica como utilizar a Atividade de Procedimento Armazenada do SQL Server para invocar um procedimento armazenado numa Base de Dados/Armazém de Dados Azure SQL a partir de um oleoduto da Data Factory.
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
author: nabhishek
ms.author: abnarain
manager: shwang
ms.custom: seo-lt-2019
ms.date: 11/27/2018
ms.openlocfilehash: bdab4f33852be6bfc2621e2cbecff76778567b1a
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "89484736"
---
# <a name="transform-data-by-using-the-sql-server-stored-procedure-activity-in-azure-data-factory"></a>Transforme os dados utilizando a atividade do Procedimento Armazenado do SqL Server na Azure Data Factory
> [!div class="op_single_selector" title1="Selecione a versão do serviço Data Factory que está a utilizar:"]
> * [Versão 1](v1/data-factory-stored-proc-activity.md)
> * [Versão atual](transform-data-using-stored-procedure.md)

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Utiliza atividades de transformação de dados num [oleoduto](concepts-pipelines-activities.md) da Data Factory para transformar e processar dados brutos em previsões e insights. A Atividade procedimentos armazenadas é uma das atividades de transformação que a Data Factory suporta. Este artigo baseia-se no artigo de transformação de [dados,](transform-data.md) que apresenta uma visão geral da transformação de dados e das atividades de transformação suportadas na Data Factory.

> [!NOTE]
> Se é novo na Azure Data Factory, leia através [da Introdução à Fábrica de Dados Azure](introduction.md) e faça o tutorial: [Tutorial: transforme dados](tutorial-transform-data-spark-powershell.md) antes de ler este artigo. 

Pode utilizar a Atividade De Procedimento Armazenada para invocar um procedimento armazenado numa das seguintes lojas de dados na sua empresa ou numa máquina virtual Azure (VM): 

- Base de Dados SQL do Azure
- Azure Synapse Analytics (anteriormente SQL Data Warehouse)
- Base de Dados do Servidor SQL.  Se estiver a utilizar o SQL Server, instale o tempo de funcionação da integração auto-hospedada na mesma máquina que hospeda a base de dados ou numa máquina separada que tenha acesso à base de dados. Self-Hosted tempo de integração é um componente que liga fontes de dados no local/em Azure VM com serviços na nuvem de forma segura e gerida. Consulte o artigo [de execução de integração auto-hospedado](create-self-hosted-integration-runtime.md) para obter detalhes.

> [!IMPORTANT]
> Ao copiar dados para a Base de Dados Azure SQL ou para o SQL Server, pode configurar o **SqlSink** na atividade de cópia para invocar um procedimento armazenado utilizando a propriedade **sqlWriterStorDProcedureName.** Para mais informações sobre a propriedade, consulte os seguintes artigos de conector: [Azure SQL Database,](connector-azure-sql-database.md) [SQL Server](connector-sql-server.md). Invocar um procedimento armazenado ao copiar dados num Azure Synapse Analytics utilizando uma atividade de cópia não é suportado. Mas, você pode usar a atividade de procedimento armazenado para invocar um procedimento armazenado em Azure Synapse Analytics. 
>
> Ao copiar dados da Base de Dados Azure SQL ou do SQL Server ou do Azure Synapse Analytics, pode configurar **o SqlSource** na atividade de cópia para invocar um procedimento armazenado para ler dados a partir da base de dados de origem utilizando a propriedade **sqlReaderStoredProcedureName.** Para mais informações, consulte os seguintes artigos de conector: [Azure SQL Database](connector-azure-sql-database.md), [SQL Server](connector-sql-server.md), [Azure Synapse Analytics](connector-azure-sql-data-warehouse.md)          

 

## <a name="syntax-details"></a>Detalhes da sintaxe
Aqui está o formato JSON para definir uma Atividade de Procedimento Armazenado:

```json
{
    "name": "Stored Procedure Activity",
    "description":"Description",
    "type": "SqlServerStoredProcedure",
    "linkedServiceName": {
        "referenceName": "AzureSqlLinkedService",
        "type": "LinkedServiceReference"
    },
    "typeProperties": {
        "storedProcedureName": "usp_sample",
        "storedProcedureParameters": {
            "identifier": { "value": "1", "type": "Int" },
            "stringData": { "value": "str1" }

        }
    }
}
```

A tabela a seguir descreve estas propriedades JSON:

| Propriedade                  | Descrição                              | Obrigatório |
| ------------------------- | ---------------------------------------- | -------- |
| name                      | Nome da atividade                     | Sim      |
| descrição               | Texto que descreve para que a atividade é usada | Não       |
| tipo                      | Para atividade de procedimento armazenado, o tipo de atividade é **SqlServerStoredProcedure** | Sim      |
| linkedServiceName         | Referência à **Base de Dados Azure SQL** ou **Azure Synapse Analytics** ou **SQL Server** registado como um serviço ligado na Data Factory. Para saber mais sobre este serviço ligado, consulte o artigo [de serviços ligados a Compute.](compute-linked-services.md) | Sim      |
| nome de procedure armazenado       | Especifique o nome do procedimento armazenado para invocar. | Sim      |
| parametrómetros de reserva armazenados | Especificar os valores dos parâmetros de procedimento armazenados. Utilize `"param1": { "value": "param1Value","type":"param1Type" }` para passar valores de parâmetros e o seu tipo suportado pela fonte de dados. Se precisar de passar nulo para um parâmetro, utilize `"param1": { "value": null }` (todos os casos inferiores). | Não       |

## <a name="parameter-data-type-mapping"></a>Mapeamento do tipo de dados de parâmetro
O tipo de dados que especifica para o parâmetro é o tipo Azure Data Factory que mapeia para o tipo de dados na fonte de dados que está a usar. Pode encontrar os mapeamentos do tipo de dados para a sua fonte de dados na área dos conectores. Alguns exemplos são

| Origem de dados          | Mapeamento do tipo de dados |
| ---------------------|-------------------|
| Azure Synapse Analytics | https://docs.microsoft.com/azure/data-factory/connector-azure-sql-data-warehouse#data-type-mapping-for-azure-sql-data-warehouse |
| Base de Dados SQL do Azure   | https://docs.microsoft.com/azure/data-factory/connector-azure-sql-database#data-type-mapping-for-azure-sql-database | 
| Oracle               | https://docs.microsoft.com/azure/data-factory/connector-oracle#data-type-mapping-for-oracle |
| SQL Server           | https://docs.microsoft.com/azure/data-factory/connector-sql-server#data-type-mapping-for-sql-server |


## <a name="error-info"></a>Informação de erro

Quando um procedimento armazenado falha e retorna os detalhes de erro, não é possível capturar a informação de erro diretamente na saída da atividade. No entanto, a Data Factory bombeia todos os seus eventos de atividade para o Azure Monitor. Entre os eventos que a Data Factory bombeia para o Azure Monitor, empurra os detalhes de erro para lá. Pode, por exemplo, configurar alertas de e-mail desses eventos. Para obter mais informações, consulte [as fábricas de dados Alert e Monitor utilizando o Azure Monitor](monitor-using-azure-monitor.md).

## <a name="next-steps"></a>Passos seguintes
Veja os seguintes artigos que explicam como transformar dados de outras formas: 

* [Atividade u-SQL](transform-data-using-data-lake-analytics.md)
* [Atividade da Colmeia](transform-data-using-hadoop-hive.md)
* [Atividade do Porco](transform-data-using-hadoop-pig.md)
* [Atividade mapReduce](transform-data-using-hadoop-map-reduce.md)
* [Atividade de streaming de Hadoop](transform-data-using-hadoop-streaming.md)
* [Atividade de Faísca](transform-data-using-spark.md)
* [Atividade personalizada do .NET](transform-data-using-dotnet-custom-activity.md)
* [Atividade de execução de Bach de Aprendizagem Automática](transform-data-using-machine-learning.md)
* [Atividade de procedimento armazenado](transform-data-using-stored-procedure.md)
