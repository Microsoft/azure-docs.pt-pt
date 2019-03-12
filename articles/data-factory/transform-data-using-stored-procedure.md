---
title: Transformar dados com a atividade de procedimento armazenado no Azure Data Factory | Documentos da Microsoft
description: Explica como utilizar a atividade de procedimento armazenado do SQL Server para invocar um procedimento armazenado de um armazém de banco de dados SQL do Azure a partir de um pipeline do Data Factory.
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 11/27/2018
author: nabhishek
ms.author: abnarain
manager: craigg
ms.openlocfilehash: 806654b7586895b62b014a49b8b3a00fb18f008f
ms.sourcegitcommit: 30a0007f8e584692fe03c0023fe0337f842a7070
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/07/2019
ms.locfileid: "57575899"
---
# <a name="transform-data-by-using-the-sql-server-stored-procedure-activity-in-azure-data-factory"></a>Transformar dados com a atividade Stored Procedure do SQL Server no Azure Data Factory
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Versão 1](v1/data-factory-stored-proc-activity.md)
> * [Versão atual](transform-data-using-stored-procedure.md)

Utilize atividades de transformação de dados numa fábrica de dados [pipeline](concepts-pipelines-activities.md) para transformar e processar dados não processados em predições e informações. A atividade de procedimento armazenado é uma das atividades de transformação que o Data Factory suporta. Este artigo baseia-se a [transformar dados](transform-data.md) artigo, que apresenta uma visão geral de transformação de dados e as atividades de transformação suportados no Data Factory.

> [!NOTE]
> Se estiver familiarizado com o Azure Data Factory, leia [introdução ao Azure Data Factory](introduction.md) e fazer o tutorial: [Tutorial: transformar dados](tutorial-transform-data-spark-powershell.md) antes de ler este artigo. 

Pode usar a atividade de procedimento armazenado para invocar um procedimento armazenado em um dos seguintes arquivos de dados na sua empresa ou numa máquina virtual do Azure (VM): 

- Base de Dados SQL do Azure
- Azure SQL Data Warehouse
- Base de dados do SQL Server.  Se estiver a utilizar o SQL Server, instale o integration runtime autoalojado no mesmo computador que aloja a base de dados ou num computador separado que tenha acesso à base de dados. Runtime de integração Autoalojado é um componente que liga os dados de origens no local/na VM do Azure com os serviços cloud de forma segura e gerida. Ver [integration runtime autoalojado](create-self-hosted-integration-runtime.md) artigo para obter detalhes.

> [!IMPORTANT]
> Quando se copiam dados na base de dados do Azure SQL ou SQL Server, pode configurar o **SqlSink** na atividade de cópia para invocar um procedimento armazenado utilizando o **sqlWriterStoredProcedureName** propriedade. Para obter detalhes sobre a propriedade, veja a seguir os artigos de conector: [Base de dados SQL do Azure](connector-azure-sql-database.md), [do SQL Server](connector-sql-server.md). Invocar um procedimento armazenado ao copiar dados para um Azure SQL Data Warehouse, utilizando uma atividade de cópia não é suportada. No entanto, pode usar a atividade de procedimento armazenado para invocar um procedimento armazenado num SQL Data Warehouse. 
>
> Quando se copiam dados a partir da base de dados do Azure SQL ou SQL Server ou o Azure SQL Data Warehouse, pode configurar **SqlSource** na atividade de cópia para invocar um procedimento armazenado para ler dados da base de dados de origem com o  **sqlReaderStoredProcedureName** propriedade. Para obter mais informações, consulte os seguintes artigos de conector: [Base de dados SQL do Azure](connector-azure-sql-database.md), [do SQL Server](connector-sql-server.md), [armazém de dados SQL do Azure](connector-azure-sql-data-warehouse.md)          

 

## <a name="syntax-details"></a>Detalhes da sintaxe
Este é o formato JSON para a definição de uma atividade de procedimento armazenado:

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
| descrição               | Texto que descreve o que a atividade é utilizada para | Não       |
| tipo                      | Para a atividade de procedimento armazenado, o tipo de atividade é **SqlServerStoredProcedure** | Sim      |
| linkedServiceName         | Referência para o **Azure SQL Database** ou **Azure SQL Data Warehouse** ou **do SQL Server** registado como um serviço ligado no Data Factory. Para saber mais sobre este serviço ligado, veja [serviços ligados de computação](compute-linked-services.md) artigo. | Sim      |
| storedProcedureName       | Especifique o nome do procedimento armazenado para invocar. | Sim      |
| storedProcedureParameters | Especifique os valores para parâmetros do procedimento armazenado. Utilize `"param1": { "value": "param1Value","type":"param1Type" }` passar valores de parâmetro e o respetivo tipo suportado pela origem de dados. Se precisar passar nulo para um parâmetro, use `"param1": { "value": null }` (todas as letras minúsculas). | Não       |

## <a name="error-info"></a>Informações do erro

Quando um procedimento armazenado falha e devolve detalhes do erro, não é possível capturar as informações de erro diretamente na saída da atividade. No entanto, o Data Factory bombeia todos de sua atividade execute eventos para o Azure Monitor. Entre os eventos que bombeia de fábrica de dados para o Azure Monitor, ela envia por push detalhes do erro lá. Pode, por exemplo, configurar alertas por e-mail desses eventos. Para mais informações, veja [de alertas e monitorizar fábricas de dados através do Azure Monitor](monitor-using-azure-monitor.md).

## <a name="next-steps"></a>Passos Seguintes
Consulte os seguintes artigos que explicam como transformar dados de outras formas: 

* [Atividade U-SQL](transform-data-using-data-lake-analytics.md)
* [Atividade do Hive](transform-data-using-hadoop-hive.md)
* [Atividade PIg](transform-data-using-hadoop-pig.md)
* [Atividade MapReduce](transform-data-using-hadoop-map-reduce.md)
* [Atividade de transmissão em fluxo do Hadoop](transform-data-using-hadoop-streaming.md)
* [Atividade do Spark](transform-data-using-spark.md)
* [Atividade personalizada do .NET](transform-data-using-dotnet-custom-activity.md)
* [Atividade de execução de Bach Machine Learning](transform-data-using-machine-learning.md)
* [Atividade de procedimento armazenado](transform-data-using-stored-procedure.md)
