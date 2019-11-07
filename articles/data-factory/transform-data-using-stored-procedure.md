---
title: Transformar dados usando a atividade de procedimento armazenado no Azure Data Factory
description: Explica como usar SQL Server atividade de procedimento armazenado para invocar um procedimento armazenado em um banco de dados SQL do Azure/data warehouse de um pipeline Data Factory.
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 11/27/2018
author: nabhishek
ms.author: abnarain
manager: craigg
ms.openlocfilehash: 5ebb2b9cdcbef59e07476dbebd289bb4402ca5fa
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/06/2019
ms.locfileid: "73683716"
---
# <a name="transform-data-by-using-the-sql-server-stored-procedure-activity-in-azure-data-factory"></a>Transformar dados usando a SQL Server atividade de procedimento armazenado no Azure Data Factory
> [!div class="op_single_selector" title1="Selecione a versão do serviço de Data Factory que você está usando:"]
> * [Versão 1](v1/data-factory-stored-proc-activity.md)
> * [Versão atual](transform-data-using-stored-procedure.md)

Você usa atividades de transformação de dados em um [pipeline](concepts-pipelines-activities.md) data Factory para transformar e processar dados brutos em previsões e ideias. A atividade de procedimento armazenado é uma das atividades de transformação às quais Data Factory dá suporte. Este artigo se baseia no artigo [transformar dados](transform-data.md) , que apresenta uma visão geral da transformação de dados e das atividades de transformação com suporte no data Factory.

> [!NOTE]
> Se você for novo no Azure Data Factory, leia a [introdução ao Azure data Factory](introduction.md) e faça o tutorial: [tutorial: transformar dados](tutorial-transform-data-spark-powershell.md) antes de ler este artigo. 

Você pode usar a atividade de procedimento armazenado para invocar um procedimento armazenado em um dos seguintes repositórios de dados em sua empresa ou em uma VM (máquina virtual) do Azure: 

- Base de Dados SQL do Azure
- Azure SQL Data Warehouse
- SQL Server banco de dados.  Se você estiver usando SQL Server, instale o tempo de execução de integração auto-hospedado no mesmo computador que hospeda o banco de dados ou em um computador separado que tenha acesso ao banco de dados. O tempo de execução de integração auto-hospedado é um componente que conecta fontes de dados locais/na VM do Azure com serviços de nuvem de maneira segura e gerenciada. Confira o artigo de [tempo de execução de integração auto-hospedado](create-self-hosted-integration-runtime.md) para obter detalhes.

> [!IMPORTANT]
> Durante a cópia de dados no Azure SQL Database ou SQL Server, você pode configurar o **sqlsink** na atividade de cópia para invocar um procedimento armazenado usando a propriedade **sqlWriterStoredProcedureName** . Para obter detalhes sobre a propriedade, consulte os seguintes artigos de conector: [banco de dados SQL do Azure](connector-azure-sql-database.md), [SQL Server](connector-sql-server.md). Não há suporte para invocar um procedimento armazenado ao copiar dados em uma SQL Data Warehouse do Azure usando uma atividade de cópia. Mas, você pode usar a atividade de procedimento armazenado para invocar um procedimento armazenado em um SQL Data Warehouse. 
>
> Ao copiar dados do Azure SQL Database ou SQL Server ou do SQL Data Warehouse do Azure, você pode configurar **sqlsource** na atividade de cópia para invocar um procedimento armazenado para ler dados do banco de dados de origem usando o **sqlReaderStoredProcedureName** Propriedade. Para obter mais informações, consulte os seguintes artigos de conector: [banco de dados SQL do Azure](connector-azure-sql-database.md), [SQL Server](connector-sql-server.md), [Azure SQL data warehouse](connector-azure-sql-data-warehouse.md)          

 

## <a name="syntax-details"></a>Detalhes da sintaxe
Este é o formato JSON para definir uma atividade de procedimento armazenado:

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

A tabela a seguir descreve essas propriedades JSON:

| Propriedade                  | Descrição                              | Necessário |
| ------------------------- | ---------------------------------------- | -------- |
| nome                      | Nome da atividade                     | Sim      |
| descrição               | Texto que descreve para que a atividade é usada | Não       |
| tipo                      | Para a atividade de procedimento armazenado, o tipo de atividade é **SqlServerStoredProcedure** | Sim      |
| linkedServiceName         | Referência ao **banco de dados SQL do Azure** ou ao **azure SQL data warehouse** ou **SQL Server** registrado como um serviço vinculado no data Factory. Para saber mais sobre esse serviço vinculado, consulte o artigo [Serviços vinculados de computação](compute-linked-services.md) . | Sim      |
| storedProcedureName       | Especifique o nome do procedimento armazenado a ser invocado. | Sim      |
| storedProcedureParameters | Especifique os valores para parâmetros de procedimento armazenado. Use `"param1": { "value": "param1Value","type":"param1Type" }` para passar valores de parâmetro e seu tipo com suporte pela fonte de dados. Se você precisar passar NULL para um parâmetro, use `"param1": { "value": null }` (todas as letras minúsculas). | Não       |

## <a name="parameter-data-type-mapping"></a>Mapeamento de tipo de dados de parâmetro
O tipo de dados especificado para o parâmetro é o tipo de Azure Data Factory que é mapeado para o tipo de dados na fonte de dados que você está usando. Você pode encontrar os mapeamentos de tipo de dados para sua fonte de dados na área conectores. Alguns exemplos são

| Fonte de dados          | Mapeamento de tipo de dados |
| ---------------------|-------------------|
| Azure SQL Data Warehouse | https://docs.microsoft.com/azure/data-factory/connector-azure-sql-data-warehouse#data-type-mapping-for-azure-sql-data-warehouse |
| Base de Dados SQL do Azure   | https://docs.microsoft.com/azure/data-factory/connector-azure-sql-database#data-type-mapping-for-azure-sql-database | 
| Oracle               | https://docs.microsoft.com/azure/data-factory/connector-oracle#data-type-mapping-for-oracle |
| SQL Server           | https://docs.microsoft.com/azure/data-factory/connector-sql-server#data-type-mapping-for-sql-server |


## <a name="error-info"></a>Informações do erro

Quando um procedimento armazenado falha e retorna detalhes do erro, você não pode capturar as informações do erro diretamente na saída da atividade. No entanto, Data Factory bombas todos os eventos de execução de sua atividade para Azure Monitor. Entre os eventos que Data Factory bombas Azure Monitor, ele envia detalhes do erro por push. Você pode, por exemplo, configurar alertas de email a partir desses eventos. Para obter mais informações, consulte [alertas e monitorar fábricas de dados usando Azure monitor](monitor-using-azure-monitor.md).

## <a name="next-steps"></a>Passos seguintes
Consulte os seguintes artigos que explicam como transformar dados de outras maneiras: 

* [Atividade de U-SQL](transform-data-using-data-lake-analytics.md)
* [Atividade do hive](transform-data-using-hadoop-hive.md)
* [Atividade Pig](transform-data-using-hadoop-pig.md)
* [Atividade MapReduce](transform-data-using-hadoop-map-reduce.md)
* [Atividade de streaming do Hadoop](transform-data-using-hadoop-streaming.md)
* [Atividade do Spark](transform-data-using-spark.md)
* [Atividade personalizada do .NET](transform-data-using-dotnet-custom-activity.md)
* [Machine Learning atividade de execução Bach](transform-data-using-machine-learning.md)
* [Atividade de procedimento armazenado](transform-data-using-stored-procedure.md)
