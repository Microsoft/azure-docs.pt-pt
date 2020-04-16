---
title: Transformar dados utilizando a atividade do Procedimento Armazenado
description: Explica como utilizar a Atividade de Procedimento Armazenada do Servidor SQL para invocar um procedimento armazenado numa Base de Dados/Armazém de Dados Azure SQL a partir de um pipeline data factory.
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
ms.openlocfilehash: 57bf653aa3f421ae8897c4be661ceef589fcdc06
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/16/2020
ms.locfileid: "81418818"
---
# <a name="transform-data-by-using-the-sql-server-stored-procedure-activity-in-azure-data-factory"></a>Transforme os dados utilizando a atividade do Procedimento Armazenado do Servidor SQL na Fábrica de Dados Azure
> [!div class="op_single_selector" title1="Selecione a versão do serviço Data Factory que está a utilizar:"]
> * [Versão 1](v1/data-factory-stored-proc-activity.md)
> * [Versão atual](transform-data-using-stored-procedure.md)

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Utiliza atividades de transformação de dados num [oleoduto](concepts-pipelines-activities.md) data factory para transformar e processar dados brutos em previsões e insights. A Atividade de Procedimento Armazenado é uma das atividades de transformação que a Data Factory suporta. Este artigo baseia-se no artigo de transformação de [dados,](transform-data.md) que apresenta uma visão geral da transformação de dados e das atividades de transformação apoiadas na Data Factory.

> [!NOTE]
> Se é novo na Azure Data Factory, leia através da [Introdução à Azure Data Factory](introduction.md) e faça o tutorial: [Tutorial: transforme dados](tutorial-transform-data-spark-powershell.md) antes de ler este artigo. 

Pode utilizar a Atividade do Procedimento Armazenado para invocar um procedimento armazenado numa das seguintes lojas de dados da sua empresa ou numa máquina virtual Azure (VM): 

- Base de Dados SQL do Azure
- Azure SQL Data Warehouse
- Base de dados do Servidor SQL.  Se estiver a utilizar o SQL Server, instale o tempo de execução de integração auto-hospedado na mesma máquina que acolhe a base de dados ou numa máquina separada que tenha acesso à base de dados. O tempo de execução de integração auto-hospedado é um componente que liga fontes de dados no local/no Azure VM com serviços na nuvem de forma segura e gerida. Consulte o artigo de tempo de execução de [integração auto-hospedado](create-self-hosted-integration-runtime.md) para mais detalhes.

> [!IMPORTANT]
> Ao copiar dados para a Base de Dados Azure SQL ou para o Servidor SQL, pode configurar o **SqlSink** na atividade de cópia para invocar um procedimento armazenado utilizando a propriedade **sqlWriterStoredProcedureName.** Para mais detalhes sobre a propriedade, consulte os seguintes artigos de conector: [Base de Dados Azure SQL,](connector-azure-sql-database.md) [Servidor SQL](connector-sql-server.md). Invocar um procedimento armazenado enquanto copia dados num Armazém de Dados Azure SQL utilizando uma atividade de cópia não é suportado. Mas pode utilizar a atividade do procedimento armazenado para invocar um procedimento armazenado num Armazém de Dados SQL. 
>
> Ao copiar dados da Base de Dados Azure SQL ou do SQL Server ou do Azure SQL Data Warehouse, pode configurar o **SqlSource** na atividade de cópia para invocar um procedimento armazenado para ler dados da base de dados de origem utilizando a propriedade **SqlReaderStoredProcedureName.** Para mais informações, consulte os seguintes artigos de conector: Base de [Dados Azure SQL,](connector-azure-sql-database.md) [Servidor SQL,](connector-sql-server.md)Armazém de [Dados Azure SQL](connector-azure-sql-data-warehouse.md)          

 

## <a name="syntax-details"></a>Detalhes da sintaxe
Aqui está o formato JSON para definir uma Atividade de Procedimento Armazenada:

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

A tabela seguinte descreve estas propriedades JSON:

| Propriedade                  | Descrição                              | Necessário |
| ------------------------- | ---------------------------------------- | -------- |
| nome                      | Nome da atividade                     | Sim      |
| descrição               | Texto descrevendo para que a atividade é usada | Não       |
| tipo                      | Para a atividade do procedimento armazenado, o tipo de atividade é **SqlServerStoredProcedure** | Sim      |
| linkedServiceName         | Referência à Base de **Dados Azure SQL** ou **Azure SQL Data Warehouse** ou **SQL Server** registado como um serviço ligado na Data Factory. Para conhecer este serviço ligado, consulte o artigo de [serviços ligados à Compute.](compute-linked-services.md) | Sim      |
| nome de procedimento armazenado       | Especifique o nome do procedimento armazenado para invocar. | Sim      |
| parâmetros de procedimento saqueados | Especifique os valores dos parâmetros de procedimento armazenados. Utilize `"param1": { "value": "param1Value","type":"param1Type" }` para passar os valores dos parâmetros e o seu tipo suportado pela fonte de dados. Se precisar de passar nulo para `"param1": { "value": null }` um parâmetro, utilize (todos os casos inferiores). | Não       |

## <a name="parameter-data-type-mapping"></a>Mapeamento do tipo de dados do parâmetro
O tipo de dados que especifica para o parâmetro é o tipo Azure Data Factory que mapeia para o tipo de dados na fonte de dados que está a utilizar. Pode encontrar os mapeamentos do tipo de dados para a sua fonte de dados na área dos conectores. Alguns exemplos são

| Origem de Dados          | Mapeamento do tipo de dados |
| ---------------------|-------------------|
| Azure SQL Data Warehouse | https://docs.microsoft.com/azure/data-factory/connector-azure-sql-data-warehouse#data-type-mapping-for-azure-sql-data-warehouse |
| Base de Dados SQL do Azure   | https://docs.microsoft.com/azure/data-factory/connector-azure-sql-database#data-type-mapping-for-azure-sql-database | 
| Oracle               | https://docs.microsoft.com/azure/data-factory/connector-oracle#data-type-mapping-for-oracle |
| SQL Server           | https://docs.microsoft.com/azure/data-factory/connector-sql-server#data-type-mapping-for-sql-server |


## <a name="error-info"></a>Informação de erro

Quando um procedimento armazenado falha e devolve detalhes de erro, não é possível capturar a informação de erro diretamente na saída de atividade. No entanto, a Data Factory bombeia todos os eventos de execução de atividade para o Monitor Azure. Entre os eventos que a Data Factory bombeia para o Monitor Azure, coloca ali detalhes de erro. Pode, por exemplo, configurar alertas de e-mail desses eventos. Para mais informações, consulte as fábricas de dados Alert e Monitor utilizando o [Monitor Azure](monitor-using-azure-monitor.md).

## <a name="next-steps"></a>Passos seguintes
Consulte os seguintes artigos que explicam como transformar dados de outras formas: 

* [Atividade U-SQL](transform-data-using-data-lake-analytics.md)
* [Atividade da Colmeia](transform-data-using-hadoop-hive.md)
* [Atividade do Porco](transform-data-using-hadoop-pig.md)
* [MapReduce Atividade](transform-data-using-hadoop-map-reduce.md)
* [Atividade de streaming de hadoop](transform-data-using-hadoop-streaming.md)
* [Atividade de Faísca](transform-data-using-spark.md)
* [Atividade personalizada do .NET](transform-data-using-dotnet-custom-activity.md)
* [Atividade de execução de Bach de aprendizagem automática](transform-data-using-machine-learning.md)
* [Atividade de procedimento armazenado](transform-data-using-stored-procedure.md)
