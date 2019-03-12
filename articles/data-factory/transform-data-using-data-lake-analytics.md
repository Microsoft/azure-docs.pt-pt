---
title: Transformar dados com o script de U-SQL - Azure | Documentos da Microsoft
description: Saiba como processar ou transformar dados ao executar scripts U-SQL no serviço de computação do Azure Data Lake Analytics.
services: data-factory
documentationcenter: ''
author: nabhishek
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 08/01/2018
ms.author: abnarain
ms.openlocfilehash: d5b074fcf182bcc9bf4dc17ba21215d27e13cbdd
ms.sourcegitcommit: 5fbca3354f47d936e46582e76ff49b77a989f299
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/12/2019
ms.locfileid: "57760975"
---
# <a name="transform-data-by-running-u-sql-scripts-on-azure-data-lake-analytics"></a>Transformar dados ao executar scripts U-SQL no Azure Data Lake Analytics 
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Versão 1](v1/data-factory-usql-activity.md)
> * [Versão atual](transform-data-using-data-lake-analytics.md)

Um pipeline de uma fábrica de dados do Azure processa os dados nos serviços de armazenamento ligada com os serviços ligados de computação. Ela contém uma sequência de atividades em que cada atividade executa uma operação de processamento específico. Este artigo descreve a **atividade de U-SQL do Data Lake Analytics** que executa um **U-SQL** script num **Azure Data Lake Analytics** serviço ligado de computação. 

Crie uma conta do Azure Data Lake Analytics antes de criar um pipeline com uma atividade de U-SQL do Data Lake Analytics. Para saber mais sobre o Azure Data Lake Analytics, consulte [introdução ao Azure Data Lake Analytics](../data-lake-analytics/data-lake-analytics-get-started-portal.md).


## <a name="azure-data-lake-analytics-linked-service"></a>Serviço do Azure Data Lake Analytics ligado
Criar uma **do Azure Data Lake Analytics** serviço para uma fábrica de dados do Azure de computação do serviço ligado para ligar um Azure Data Lake Analytics. Este serviço ligado refere-se a atividade de U-SQL do Data Lake Analytics no pipeline. 

A tabela seguinte fornece descrições para as propriedades genéricas usadas na definição de JSON. 

| Propriedade                 | Descrição                              | Necessário                                 |
| ------------------------ | ---------------------------------------- | ---------------------------------------- |
| **tipo**                 | A propriedade de tipo deve ser definida como: **AzureDataLakeAnalytics**. | Sim                                      |
| **accountName**          | Nome da conta do Azure Data Lake Analytics.  | Sim                                      |
| **dataLakeAnalyticsUri** | URI do Azure Data Lake Analytics.           | Não                                       |
| **subscriptionId**       | ID de subscrição do Azure                    | Não                                       |
| **resourceGroupName**    | Nome do grupo de recursos do Azure                | Não                                       |

### <a name="service-principal-authentication"></a>Autenticação do principal de serviço
O serviço ligado do Azure Data Lake Analytics requer uma autenticação do principal de serviço para ligar ao serviço Azure Data Lake Analytics. Para utilizar autenticação do principal de serviço, registe-se uma entidade de aplicação no Azure Active Directory (Azure AD) e conceder o acesso ao Data Lake Analytics e o Store de Lake de dados utiliza. Para obter passos detalhados, consulte [autenticação serviço a serviço](../data-lake-store/data-lake-store-authenticate-using-active-directory.md). Tome nota dos seguintes valores, o que utilizar para definir o serviço ligado:

* ID da aplicação
* Chave da aplicação 
* ID do inquilino

Conceder permissão de principal de serviço para o seu Azure Data Lake Anatlyics com o [Assistente para adicionar utilizador](../data-lake-analytics/data-lake-analytics-manage-use-portal.md#add-a-new-user).

Utilize autenticação do principal de serviço ao especificar as seguintes propriedades:

| Propriedade                | Descrição                              | Necessário |
| :---------------------- | :--------------------------------------- | :------- |
| **servicePrincipalId**  | Especifique o ID de cliente. da aplicação     | Sim      |
| **servicePrincipalKey** | Especifique a chave da aplicação.           | Sim      |
| **tenant**              | Especifique as informações de inquilino (inquilino ou nome do ID de domínio) em que reside a aplicação. Pode recuperá-la ao pairar o cursor do rato no canto superior direito do portal do Azure. | Sim      |

**Exemplo: Autenticação do principal de serviço**
```json
{
    "name": "AzureDataLakeAnalyticsLinkedService",
    "properties": {
        "type": "AzureDataLakeAnalytics",
        "typeProperties": {
            "accountName": "<account name>",
            "dataLakeAnalyticsUri": "<azure data lake analytics URI>",
            "servicePrincipalId": "<service principal id>",
            "servicePrincipalKey": {
                "value": "<service principal key>",
                "type": "SecureString"
            },
            "tenant": "<tenant info, e.g. microsoft.onmicrosoft.com>",
            "subscriptionId": "<optional, subscription id of ADLA>",
            "resourceGroupName": "<optional, resource group name of ADLA>"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }       
    }
}
```

Para saber mais sobre o serviço ligado, veja [serviços ligados de computação](compute-linked-services.md).

## <a name="data-lake-analytics-u-sql-activity"></a>Atividade de U-SQL do Data Lake Analytics
O seguinte fragmento JSON define um pipeline com uma atividade de U-SQL do Data Lake Analytics. Definição da atividade possui uma referência ao serviço ligado do Azure Data Lake Analytics que criou anteriormente. Para executar um script de U-SQL do Data Lake Analytics, o Data Factory submete o script que especificou para o Data Lake Analytics e as entradas necessárias e saídas é definida no script para buscar e de saída do Data Lake Analytics. 

```json
{
    "name": "ADLA U-SQL Activity",
    "description": "description",
    "type": "DataLakeAnalyticsU-SQL",
    "linkedServiceName": {
        "referenceName": "<linked service name of Azure Data Lake Analytics>",
        "type": "LinkedServiceReference"
    },
    "typeProperties": {
        "scriptLinkedService": {
            "referenceName": "<linked service name of Azure Data Lake Store or Azure Storage which contains the U-SQL script>",
            "type": "LinkedServiceReference"
        },
        "scriptPath": "scripts\\kona\\SearchLogProcessing.txt",
        "degreeOfParallelism": 3,
        "priority": 100,
        "parameters": {
            "in": "/datalake/input/SearchLog.tsv",
            "out": "/datalake/output/Result.tsv"
        }
    }   
}
```

A tabela seguinte descreve os nomes e descrições das propriedades que são específicas para esta atividade. 

| Propriedade            | Descrição                              | Necessário |
| :------------------ | :--------------------------------------- | :------- |
| nome                | Nome da atividade no pipeline     | Sim      |
| descrição         | Texto que descreve o que faz a atividade.  | Não       |
| tipo                | Para a atividade de U-SQL do Data Lake Analytics, é o tipo de atividade **DataLakeAnalyticsU SQL**. | Sim      |
| linkedServiceName   | Serviço ligado ao Azure Data Lake Analytics. Para saber mais sobre este serviço ligado, veja [serviços ligados de computação](compute-linked-services.md) artigo.  |Sim       |
| scriptPath          | Caminho para a pasta que contém o script de U-SQL. Nome do ficheiro diferencia maiúsculas de minúsculas. | Sim      |
| scriptLinkedService | Serviço que liga ligado a **do Azure Data Lake Store** ou **armazenamento do Azure** que contém o script para a fábrica de dados | Sim      |
| degreeOfParallelism | O número máximo de nós em simultâneo utilizada para executar a tarefa. | Não       |
| prioridade            | Determina quais os trabalhos em fila de espera devem ser selecionados para executar primeiro. Menor o número, maior será a prioridade. | Não       |
| parâmetros          | Parâmetros a transmitir para o script de U-SQL.    | Não       |
| runtimeVersion      | Versão de tempo de execução do mecanismo de U-SQL para utilizar. | Não       |
| compilationMode     | <p>Modo de compilação do U-SQL. Tem de ser um dos seguintes valores: **Semântica:** Executar apenas verificações semânticas e verificações de sanidade necessários, **completa:** Executar a compilação completa, incluindo a verificação de sintaxe, otimização, geração de código, etc., **SingleBox:** Execute a compilação completa, com a definição de TargetType para SingleBox. Se não especificar um valor para esta propriedade, o servidor determina o modo de compilação ideal. | Não |

Ver [SearchLogProcessing.txt](#sample-u-sql-script) para a definição de script. 

## <a name="sample-u-sql-script"></a>Exemplo de script de U-SQL

```
@searchlog =
    EXTRACT UserId          int,
            Start           DateTime,
            Region          string,
            Query           string,
            Duration        int,
            Urls            string,
            ClickedUrls     string
    FROM @in
    USING Extractors.Tsv(nullEscape:"#NULL#");

@rs1 =
    SELECT Start, Region, Duration
    FROM @searchlog
WHERE Region == "en-gb";

@rs1 =
    SELECT Start, Region, Duration
    FROM @rs1
    WHERE Start <= DateTime.Parse("2012/02/19");

OUTPUT @rs1   
    TO @out
      USING Outputters.Tsv(quoting:false, dateTimeFormat:null);
```

No acima do exemplo de script, a entrada e saída para o script é definido no  **\@na** e  **\@horizontalmente** parâmetros. Os valores para  **\@na** e  **\@horizontalmente** parâmetros do script de U-SQL são transmitidos dinamicamente pelo Data Factory com a secção "parameters". 

Também pode especificar outras propriedades como degreeOfParallelism e prioridade na sua definição de pipeline para as tarefas que são executados no serviço Azure Data Lake Analytics.

## <a name="dynamic-parameters"></a>Parâmetros dinâmicos
A definição de pipeline de exemplo, entrada e saída parâmetros são atribuídos com valores embutidos. 

```json
"parameters": {
    "in": "/datalake/input/SearchLog.tsv",
    "out": "/datalake/output/Result.tsv"
}
```

É possível usar parâmetros dinâmicos em vez disso. Por exemplo: 

```json
"parameters": {
    "in": "/datalake/input/@{formatDateTime(pipeline().parameters.WindowStart,'yyyy/MM/dd')}/data.tsv",
    "out": "/datalake/output/@{formatDateTime(pipeline().parameters.WindowStart,'yyyy/MM/dd')}/result.tsv"
}
```

Neste caso, os ficheiros de entrada ainda são captados a partir da pasta /datalake/input e ficheiros de saída são gerados na pasta /datalake/output. Os nomes de ficheiro são dinâmicos com base na hora de início de janela que está a ser transmitida ao pipeline é ativado.  

## <a name="next-steps"></a>Passos Seguintes
Consulte os seguintes artigos que explicam como transformar dados de outras formas: 

* [Atividade do Hive](transform-data-using-hadoop-hive.md)
* [Atividade PIg](transform-data-using-hadoop-pig.md)
* [Atividade MapReduce](transform-data-using-hadoop-map-reduce.md)
* [Atividade de transmissão em fluxo do Hadoop](transform-data-using-hadoop-streaming.md)
* [Atividade do Spark](transform-data-using-spark.md)
* [Atividade personalizada do .NET](transform-data-using-dotnet-custom-activity.md)
* [Atividade de execução de lotes de Aprendizado de máquina](transform-data-using-machine-learning.md)
* [Atividade de procedimento armazenado](transform-data-using-stored-procedure.md)
